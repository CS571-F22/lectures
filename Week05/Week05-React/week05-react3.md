---
marp: true
footer: CS571 Building User Interfaces | Cole Nelson | Lecture 05: React 3
paginate: true
theme: uncover
style: |
  section {
    text-align: left
  }
  .center-info {
    text-align: center
  }
---


<br>

# **React 3**
### CS571: Building User Interfaces


<br>

#### Cole Nelson

---

### Announcements
 - Midterm on 10/25 in class (details on next slide).
 - Please contact me by 10/18 if you will not be able to make the exam (Canvas/HonorLock alternative).
 - HW5 extended to Friday, 10/21 with no penalty (final late day of 10/28). Extra credit available.
 - Thanks for your patience! Your feedback is greatly appreciated.

---

### Midterm Details
 - Tuesday, 10/25 @ 5:45p-8:15p (2.5 hours)
 - 32 questions worth 15 points covering Weeks 1-6.
   - 20 Multiple Choice (0.25 pts each)
   - 10 Short Answer (0.5 pts each)
   - 2 Long Response (2.5 pts each)
   - Half programming, half design
 - Closed book, closed internet, closed interpreter.
 - You may bring one double-sided standard size notesheet. Handwritten or digital is OK.

---

### What will we learn today?

<div>

 - `useState` gotcha!
 - `useRef` and Controlled vs Uncontrolled Components
 - Working with "complex" APIs
 - How to keep a secret? 🤫

</div>

---

# `useState` gotcha!
This should have been mentioned earlier...

---

### `useState` Gotcha

Did you know that the mutator function returned by `useState` happens asynchronously?

```javascript
setBadgermon(["Pikachu", "Squirtle", "Ivysaur"]);
console.log(badgermon);
```

In the above example, the `console.log` will show an empty array because `setBadgermon` has not been completed yet (it's async)!

---

### `useState` Gotcha

If we want to `console.log` the *updated* state, we should add a `useEffect` with `badgermon` as its dependency!

```javascript
useEffect(() => {
  console.log(badgermon)
}, [badgermon])
```

---

# React Hooks
Final hook(s) that we will be learning!

---

### `useReducer` Hook

Honorable mention...

```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

A redux-like way to manage state; change the state based on the `dispatch`.

We won't use this, but you may see it used by others!

[See example.](https://reactjs.org/docs/hooks-reference.html#usereducer)

---

### `useRef` Hook

Usually used to "reference" an input element.

```javascript
const inputVal = useRef();
return (
  <div>
    <label for="myInput">Type something here!</label>
    <input id="myInput" ref={inputVal}></input>
  </div>
);
```

The value of a ref can be retrieved via its `current` property, e.g. `inputVal.current.value`

---

### Controlled vs Uncontrolled Components

`useRef` is normally used to create a reference for an *uncontrolled* input component.

However, we can *control* an input component via its `value` and `onChange` properties.

[Example of an uncontrolled input component.](https://stackblitz.com/edit/react-dkpqwh)

[Example of a controlled input component.](https://stackblitz.com/edit/react-q134ha)

---

### Controlled vs Uncontrolled: Pros & Cons

[React generally recommends](https://reactjs.org/docs/forms.html) controlled components.

Controlled components can cause many re-renders, however uncontrolled components give you no control over the `onChange` property.

We'll practice using both.

**Note:** Performance matters with React Native!

---

# Working with Complex APIs
Beyond GETting data...

---

### Scenario
You are building a database system. What operations should you allow a developer to perform?

---

### Scenario
You are building a database system. What operations should you allow a developer to perform?

<div>

 1. **C**reate data.
 2. **R**ead data.
 3. **U**pdate data.
 4. **D**elete data.

</div>

---

### CRUD Operations via HTTP

| CRUD Operation | HTTP Operation |
| --- | --- |
| Create | POST |
| Read | GET |
| Update | PUT |
| Delete | DELETE |

---

# HTTP Intro

Data is transmitted by requests and responses that allow us to create (POST), read (GET), update (PUT), and delete (DELETE) data!

---

![bg 85%](figures/http_request.png)

<br><br><br><br><br><br><br><br><br>

[Image Source](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/http_basics.html)

---

![bg 85%](figures/http_response.png)

<br><br><br><br><br><br><br><br><br>

[Image Source](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/http_basics.html)


---

### HTTP Intro

An HTTP request may have *path* and *query* parameters

```
https://www.example.com/packers/AaronRodgers/stats?all=true&since=2010
```

Here, `AaronRodgers` is a *path* parameter while `all` and `since` are *query* parameters.

---

### HTTP Intro

HTTP requests (specifically `PUT` and `POST`) may also have a *request body*. This is located below the  headers.

```json
{
    "username": "testuser1234",
    "password": "pass123",
    "refCode": "bid_999988887777"
}
```

For a JSON body, we need an additional HTTP header.
`Content-Type: application/json`

---

### HTTP Status Codes

| HTTP Code | Response Type |
| --- | --- |
| 100s | Informational |
| 200s | Successful |
| 300s | Redirection |
| 400s | Client Error |
| 500s | Server Error |

---

### HTTP Specific Status Codes

| HTTP Code | Response |
| --- | --- |
| 200 | OK |
| 304 | Not Modified |
| 400 | Bad Request |
| 401 | Unauthorized |
| 404 | Not Found |
| 409 | Conflict |
| 413 | Request Entity Too Large |
| 500 | Internal Server Error |

---

# Reading the Docs
Go over the [HW5 API documentation](https://github.com/CS571-F22/hw5-api/blob/main/DOCUMENTATION.md).

---

### Secrets! Secrets!

Is there anything **special** about requests for logging in? Not really!

It varies from system to system, but typically we POST a username and password in the request body to an authentication endpoint.

e.g.

`POST` `/register` or
`POST` `/login`

---

### Secrets! Secrets!

These endpoints return a session for the user, in HW5 this is in the form of a [JavaScript Web Token (JWT)](https://jwt.io/).

This is a temporary, all-access token for authenticating with the API. It is used in lieu of a username and password. Why might we do this?

---

### Secrets! Secrets!

We typically store these [in an http-only cookie](https://clerk.dev/blog/how-httponly-cookies-help-mitigate-xss-attacks).

However, for HW5, we store it in a context provider and pass it as an `Authorization` header, e.g.

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwidXNlcm5hbWUiOiJ0ZXN0dXNlcjEyMzQiLCJpYXQiOjE2NjU0NjU1MjMsImV4cCI6MTY2NTQ2OTEyM30.lbanHn0Is9ghZc3q9XQD5D3Ff9820Ad_DHH-t29txfo
```

---

# How can we do this?
cURL, Postman, and fetch!

---

### cURL

![bg 85%](figures/curl.png)

<br><br><br><br><br><br><br>

[Image Source](https://itslinuxfoss.com/how-to-make-a-post-request-with-curl/)

---

### Postman

![bg right 96%](figures/postman.jpg)

[www.postman.com](https://www.postman.com/)

<br>

[Image Source](https://twitter.com/PR0GRAMMERHUM0R/status/1574957096751308800)

---

# Postman Demo
[Using the HW5 API](https://github.com/CS571-F22/hw5-api/blob/main/DOCUMENTATION.md)

---

### Fetching w/ `POST`, `PUT`, and `DELETE`
`fetch` [can do a lot more](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) than just retrieving data.

<div>

 - specify request method
 - specify request headers
 - specify request body
 - inspect response status
 - inspect response headers
 - inspect response body
 - ...and so much more!

</div>

---

```javascript
fetch("https://example.com/create-content", {
    method: "POST",
    headers: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify({
        content: "Hello World!"
    })
}).then(res => {
    if (res.status === 409) {
        alert("This content already exists!")
    }
    return res.json();
}).then(json => {
    if (json.msg) {
        alert(json.msg)
    }
});
```

---

# `fetch` Demo
Same thing as in Postman, but with `fetch`

---

### What is this "HTTPS" I hear about?

The "secure" version of HTTP.

Same thing as the HTTP protocol with end-to-end encryption. We use HTTPS for our API.

---

### What did we learn today?

<div>

 - `useState` gotcha!
 - `useRef` and Controlled vs Uncontrolled Components
 - Working with "complex" APIs
 - How to keep a secret? 🤫

</div>

---

# On to Interaction Design! 🚀
