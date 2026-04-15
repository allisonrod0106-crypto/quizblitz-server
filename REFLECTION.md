## Question 1: 
Middleware in Express is code that runs during the request-response cycle before the final route handler. It can modify the request, validate data, or block access. Middleware functions take `req`, `res`, and `next`, and must call `next()` to continue the chain.

In my QuizBlitz server, I use global middleware like `app.use(express.json())` to parse JSON bodies and `app.use(cors())` to allow frontend requests. I also use route-level middleware such as `verifyToken` on protected routes like `POST /api/scores`.

Order matters because Express runs middleware from top to bottom. For example, `express.json()` must run before routes that access `req.body`, and `verifyToken` must run before protected route logic. Global middleware applies to all routes, while route-level middleware only runs on specific endpoints where it is explicitly included.

## Question 2:
Passwords must never be stored in plain text because if the database is compromised, attackers would immediately gain access to all user accounts. Since many users reuse passwords, this could lead to broader security breaches beyond the app.

When calling `bcrypt.hash(password, 10)`, bcrypt creates a secure, one-way hash of the password and automatically adds a random salt. This ensures that even identical passwords produce different hashes. The number 10 is the cost factor, which controls how computationally expensive the hashing process is. Higher values increase security by slowing down brute-force attacks.

`bcrypt.compare()` works by hashing the entered password using the same salt stored in the hash and comparing the results. It does not reverse the hash, since hashing is one-way. Instead, it verifies whether the computed hash matches the stored one.

## Question 3
When a user registers, the client sends a username and password. The server hashes the password with bcrypt and stores the user in the database. On login, the client sends credentials again, and the server verifies them using `bcrypt.compare()`. If valid, the server generates a JWT and returns it to the client.

The JWT contains user information such as a user ID or username and is signed with a secret key. When the user submits a score, the client includes the token in the Authorization header. The server verifies the token using middleware like `verifyToken`, extracts the user data, and allows the request.

The server does not need to query the database to verify the token because the JWT is self-contained and signed. This allows authentication to be stateless and efficient.

## Question 4:
Using an in-memory array like `let scores = []` has major limitations. First, all data is lost whenever the server restarts, meaning users would lose their scores. Second, the data is not shared across multiple server instances, making it unsuitable for scaling or deployment.

Switching to MongoDB solves both issues. Data is stored persistently, so it remains even if the server restarts or crashes. It also allows multiple servers to access the same data, supporting scalability.

If the server is redeployed, MongoDB data remains intact because it is stored externally, such as in MongoDB Atlas. This is different from the in-memory array, which exists only during runtime and is reset every time the server restarts.

## Question 5:
Having `GET /api/scores` as public and `POST /api/scores` as protected makes sense for a leaderboard app. Viewing scores is a read-only action that should be accessible to all users, even without an account. This encourages engagement and allows users to explore the leaderboard easily.

Submitting scores, however, must be protected to prevent abuse, such as fake submissions or spam. Requiring authentication ensures that only verified users can submit scores and that each score can be linked to a user.

If `GET /api/scores` required authentication, it would reduce usability. Users would need to log in just to view the leaderboard, creating unnecessary friction and discouraging casual users from interacting with the app.
