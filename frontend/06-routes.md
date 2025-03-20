## Routes

---

This project uses React Router. You still need this for Vite. Next.JS is a full-stack solution, so no need for it there.
This is just for separate pages, which you may or may not want.
Website still remains SPA. Just mimics the behavior of another page.

`App.js`
This is where you will import a lot of the router dependecies. They all come from `react-router-dom`.
These will all go into the `return()`.
He uses `<HashRouter>`, and puts the `<Navigation>`,`<Routes>`, and `<Route>` components inside of it.

`Swap.js` & `Deposit.js` get created and imported into `App.js`.
`Swap.js` is the root route. `localhost:3000`.
`Deposit.js`

---

### Different types of Routers

`<BrowserRouter>` - recommended interface for running react router in a web browser.
We see this one often and in every react tutorial.

`<HashRouter>` is for when the URL should **NOT** be sent to the server for some reason.
I think he uses this here because it's in a tab, not a "full" route. React Docs recommend **NOT** using this.

---

### What Routes are doing

```js
<HashRouter>
  <Navigation />

  <hr />

  <Tabs />

  <Routes>
    <Route exact path="/" element={<Swap />} />
    <Route path="/deposit" element={<Deposit />} />
    <Route path="/withdraw" element={<Withdraw />} />
    <Route path="/charts" element={<Charts />} />
  </Routes>
</HashRouter>
```

So what you have here is when you show a path of something, then render this `element`.
Which is a component.

Greg creates a `Tabs.js` to hold all these different `<HashRouter>`.
He gives it a React Bootstrap Nav.
