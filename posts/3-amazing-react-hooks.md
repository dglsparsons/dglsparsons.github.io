---
title: 3 amazing REACT HOOKS to keep your code organized neatly
description: Here are the three React Hooks I have found the most useful for producing websites in the cleanest, simplest way possible. They manage localStorage, sessionStorage, authentication and asynchronous tasks.
date: 2020-11-25T09:00:00
layout: layouts/post.njk
cover: /img/remote/hooks.jpg
---

Hi, my name is Doug. I’ve been a developer for several years and now work as the Lead Engineer at Shamaazi. Over this period of time, I have written a lot of different UIs and learned a lot of ways to structure React code.

This week I wanted to share my experience of the custom React Hooks I have found the most useful for producing websites in the cleanest, simplest way possible.

## React Hooks

Hooks were first introduced to React in version 16.8, after being teased in 2018. There’s a fantastic guide introducing them on the [React website](https://reactjs.org/docs/hooks-intro.html). Simply stated, they are a way to write side-effects for functional UI components. This allows you to write parts of your UI as JavaScript functions, but still have the ability to manage state, call APIs, use storage, authenticate users, and so on.

React provides some hooks out of the box (`useState`, `useEffect` and `useContext` being the main three). On top of this, it allows you to compose your own higher-level hooks to separate out reusable logic. These custom hooks are what I’ll explore here. Here are the three I’ve found the most useful across the range of products we produce at Shamaazi.

## Performing Asynchronous Actions

Most websites have to perform some form of asynchronous actions, whether it is loading data to display on the page or submitting data based on a user’s input and actions. It’s helpful to keep a track of the status of these asynchronous actions; is it currently loading? has it returned a result? Was there an error?

We found a lot of our components started sharing a lot of similar code, either for fetching data on an initial load or for submitting data. This looked like the following:
```javascript
const MyComponent = () => {
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState(null)
  const [result, setResult] = useState(null)

  useEffect(async () => {
    setResult(null)
    setError(null)
    setLoading(true)
    try {
      const result = doSomeAction();
      setResult(result)
    } catch (e) {
      setError(e)
    } finally {
      setLoading(false)
    }
  }, [])

  if (loading) {
    return <>loading...</>
  }

  if (error) {
    return <>something broke</>
  }

  return <>{result}</>
}
```

All this loading and error logic can be pulled into a hook, making our interface much neater.

```javascript
const MyTidyComponent = () => {
  const {loading, result, error} = useAsync(doSomeAction)

  if (loading) {
    return <>loading...</>
  }

  if (error) {
    return <>something broke</>
  }

  return <>{result}</>
}
```

This `useAsync` hook is responsible for managing the loading, error and result states, removing the need for all this logic within the actual component. It also lets us reuse this throughout our application. This massively simplifies loading data onto a page.

As a bonus, we found we also wanted the ability to execute an action later, rather than just when the component is created. This is useful for performing asynchronous actions based on a user's input; actions like submitting a form can use the same hook but pass a `false` value as a second parameter. This indicates that they don’t want the action to be executed straight away.

```javascript
const { execute, loading, result, error } = useAsync(submitSomeForm, false)

<form onSubmit={execute}>
  ...
</form>
```

We also found that the hook sometimes caused a memory leak if a form submission navigated away from the component (e.g. a form might take you to the next page when it is submitted, but setting `loading` to `false` after you’ve been taken away from the form is a memory leak). We’ve handled this by tracking whether the hook is mounted on the page (tracked through `useRef`). We’ll only update any state if the component is still present. This avoids any memory leaks.

The full version of our `useAsync` hook is here:

```javascript
import { useEffect, useState, useCallback, useRef } from 'react'

export default (asyncFunction, immediate = true) => {
  const [loading, setLoading] = useState(false)
  const [result, setResult] = useState(null)
  const [error, setError] = useState(null)

  // Track a reference to whether the useAsync is actually on a mounted component.
  // useEffect below returns a cleanup that sets this to false. Before setting
  // any state, we check if the cleanup has run. If it has, don't update the state.
  const mounted = useRef(true)

  useEffect(() => {
    return () => {
      mounted.current = false
    }
  }, [])

  const execute = useCallback(async (...args) => {
    setLoading(true)
    setResult(null)
    setError(null)
    try {
      const r = await asyncFunction(...args)
      if (mounted.current) {
        setResult(r)
      }
      return r
    } catch (e) {
      if (mounted.current) {
        setError(e)
      }
    } finally {
      if (mounted.current) {
        setLoading(false)
      }
    }
  }, [asyncFunction])

  useEffect(() => {
    if (immediate) {
      execute()
    }
  }, [execute, immediate])

  return { execute, loading, result, error }
}
```

## Updating LocalStorage or SessionStorage

As part of some of our products, we populate a 'shopping basket'. This keeps a track of what a user has been doing. Sometimes, we want this to persist even if they navigate away from our site, refresh the page, or close the browser. To achieve this, we use a combination of [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) and [sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)

React itself doesn't provide any hooks for storing data in `localStorage` or `sessionStorage`, but we wanted a consistent experience with `useState`. Realistically, it shouldn't be any harder to use `localStorage` than it would be to use state normally.

For example, we might want to use `localStorage` to keep track of a user's input.
```javascript
const storageComponent = () => {
  const [value, setValue] = useLocalStorage('storage_key', 'default_value')

  return <input value={value} onChange={e => setValue(e.target.value}/>
}
```

Our hooks to achieve this look like the following:
```javascript
const useStorage = (key, initialValue, storage) => {
  // Pass initial state function to useState so logic is only executed once
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = storage.getItem(key)
      return item ? JSON.parse(item) : initialValue
    } catch (error) {
      console.error(error)
      return initialValue
    }
  })

  useEffect(() => {
    try {
      // Update storage every time the value is changed
      storage.setItem(key, JSON.stringify(storedValue))
    } catch (e) {
      console.error(e)
    }
  }, [storedValue, storage, key])

  return [storedValue, setStoredValue]
}

export const useLocalStorage = (key, initialValue) => {
  return useStorage(key, initialValue, window.localStorage)
}

export const useSessionStorage = (key, initialValue) => {
  return useStorage(key, initialValue, window.sessionStorage)
}
```

## Authenticating users

A super common scenario we've come across is having a bunch of components that all care whether a user is logged in. They often care about acting on the user too, through methods like `login`, `logout` or `resetPassword`.

In order to keep all these components in sync, we only want a single source of information about the current user. We could do this by having a component wrapping our entire application that manages a `user` state, and passes any props down to where they are used for the `user`, `login`, `logout` or `resetPassword` methods.

This quickly becomes messy though, with many components that don't really care being passed `user` `login` and `logout` props even if they don't use them themselves - only a child of theirs does.

Luckily React provides the idea of a [context](https://reactjs.org/docs/hooks-reference.html#usecontext). Allowing us to solve this problem.

We can create an Auth context, and use a hook to get any information from it we want. We can also embed our auth API calls into this context.

This would look like the following to use:
```javascript
// In our top level App.js
import { ProvideAuth } from 'hooks/useAuth'

export default () => {
  return <ProvideAuth>
    <RestOfApplication/>
    ...
  </ProvideAuth>
}
```

```javascript
// in a component that wants to use Auth
import useAuth from 'hooks/useAuth'

export default () => {
  const { user, login, logout, resetPassword } = useAuth();

  return <>
    {user}
  </>
}
```


This hook itself looks like the following:
```javascript
import React, { useCallback, useState, useEffect, useContext, createContext } from 'react'

const authContext = createContext()

// Hook for child components to get the auth object and re-render when it changes.
export default () => {
  return useContext(authContext)
}

// Provider component that wraps components and makes useAuth() available
export function ProvideAuth({ children }) {
  const auth = useAuthProvider()
  return <authContext.Provider value={auth}>{children}</authContext.Provider>
}

// Provide Auth hook that creates auth object and handles state
function useAuthProvider() {
  const [user, setUser] = useState(null)

  // Get the logged in user when created
  useEffect(() => {
    const user = getLoggedInUser()
    setUser(user)
  }, [])

  const login = async (...) => {
    const user = ...
    setUser(user)
  }

  const logout = async () => {
    ...
    setUser(null)
  }

  const resetPassword = async () => {
    ...
  }

  return {
    resetPassword
    login,
    logout,
    user
  }
}
```

This has the additional benefit of keeping all of the authentication logic together. To change to a different auth provider, we would only have to change this one file.

## Conclusion

React provides some really powerful abstractions for creating code that is neatly organised and easy to read. Here, we’ve looked at the three React Hooks I’ve found the most useful: `useAsync` for executing asynchronous actions either when a component is created or when a user performs an action, `useStorage` for using `localStorage` and `sessionStorage` in the same way as `useState`, and finally, `useAuth` for managing users and authentication.

These three hooks provide powerful abstractions that let you build React components in a simple manner.

---

Do you have any other custom React Hooks you find useful? Think I’ve missed any key ones? Please let me know.

---

Looking for other ways to keep your code organised? Check out my [article on writing IMMUTABLE code](https://dgls.dev/posts/immutability/).

---

Enjoyed this post? Want to share your thoughts on the matter? Found this article helpful? Disagree with me? Let me know by [messaging me on Twitter](https://twitter.com/dglsparsons).
