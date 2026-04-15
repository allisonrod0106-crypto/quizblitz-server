## Q1
B. `app.use(express.json())` middleware is missing or registered after the route

## Q2

A 400 status code means the client sent a request that is invalid or malformed. In my QuizBlitz server, a 400 response would be appropriate during registration or login if the user does not include a username or password in the request body. For example, if  `req.body.username` or `req.body.password` is missing, the server can return 400 Bad Request because the request does not meet the required format.

A 401 status code means the client is not authenticated. In my QuizBlitz server, a 401 response would be used in the `verifyToken` middleware. If a user tries to submit a score to `POST /api/scores` without including a JWT in the Authorization header, or if the token is invalid, the server should return 401 Unauthorized

A 404 status code means the requested resource does not exist on the server. In my QuizBlitz server, a 404 response would be appropriate if a request is made to a route that does not exist, such as `/api/score` instead of `/api/scores`.

## Q3

The problem is that `Score.find()` is asynchronous, but the code does not wait for it to finish. The query is started, but the server immediately sends the response `{ message: 'done' }` before the database returns any data. As a result, the scores are never included in the response.

Correct: 
```js
app.get('/api/scores', async (req, res) => {
  try {
    const scores = await Score.find()
      .sort({ score: -1 })
      .limit(10)

    res.json(scores)
  } catch (err) {
    res.status(500).json({ error: 'Failed to fetch scores' })
  }
})
```

## Q4
B. A schema defines the shape and validation rules for documents; a model is the class you use to query and save documents based on that schema

## Q5

One advantage of storing the JWT in a cookie is that it can be sent automatically with every request by the browser, so the client does not need to manually attach the token each time. This can simplify frontend code, especially for traditional web apps.

One advantage of using the Authorization header is that it gives the client explicit control over when and how the token is sent. It also works consistently across different platforms (web, mobile apps, APIs) and avoids issues like CSRF that can occur with cookies.

For a mobile-accessible game like QuizBlitz, the Authorization header approach is more appropriate. It is more flexible and works well across browsers, mobile apps, and APIs, which is important if the game is accessed from different environments. It also provides clearer control over authentication and is the standard approach for REST APIs.