# Authentication System Usage Guide

## Setup

Before using the authentication system, ensure that you have installed the necessary dependencies. If not, install them using npm:

```bash
npm install @knfs-tech/bamimi-auth
##or
yarn add @knfs-tech/baimmi-auth
```

## Importing Modules

```javascript
const { Auth } = require("@knfs-tech/bamimi-auth");
```

#### Config file with type 

```javascript
module.exports = {
	accessPassword: {
		idFields: ['username'], // fields id to verify, you can use with multiple ['username', 'email']
		pinField: ['password'] // field as password, you can use with other name field
	},
	tokenBasedToken: {
		accessToken: {
		secretKey: "",
		options: {}
		},
		refreshToken: {
			secretKey: "",
			options: {},
			multiple: false, // if you want to multiple refresh token, in case multiple device
			use: false // if you want to use refresh token
		},
		useBlacklist: false, // if you want to black list to block token
		// storage for save refresh token (in case using multiple) and use black list
		storage: {
			/**
			 * @type {Enum("memory", "redis")}
			 */
			type: "memory",
			options: {} // if you redis, it is connection info of redis, In code we use ioredis
		},
		// fields of origin data to create token
		fields: [
			"id",
			"username",
			"email"
		]
	},
	mfa: {
		appName: "@knfs-tech/bamimi-auth",
		fieldId: "id" // id for uri auth and Qrcode
	}
}
```
you can check by 
```javascript
const { configType } = require("@knfs-tech/bamimi-auth");
```

## Initializing the Authentication System

To initialize the authentication system, create an instance of the Auth class:

```javascript
const auth = Auth.init();
//or 
const auth = Auth.init(config);
```


## Using Authentication Functionalities

If Auth have been initialized, you can use instance and other file by 
```javascript 
const auth = Auth.getAuth();
```

Now, let's explore how to use different authentication functionalities provided by the system with a specific example:

### 1. Authenticating with Basic Auth

Suppose you receive an HTTP request with Basic Authentication credentials in the Authorization header. You can authenticate the user with Basic Auth as follows:

```javascript
const authorizationHeader = req.headers.authorization; // Get Authorization header from request
const userData = await getUserDataFromDatabase(); // Retrieve user data from your database
const isAuthenticated = await auth.verifyWithBasicAuth(userData, authorizationHeader);
```

### 2. Generating Multi-Factor Authentication (MFA)

Suppose you want to generate a QR code for MFA setup for a specific user. You can do it as follows:

```javascript
const originalData = { id: "user_id_here" }; // User data for which MFA is to be set up
const qrCodeUrl = await auth.generateMFA(originalData, RETURN_TYPE.MFA.URL);
```

### 3. Verifying Password and Generating JWT Tokens

Suppose you want to verify a user's password and generate JWT tokens for authentication. You can do it as follows:

```javascript
const originalData = { username: "example_user", password: "example_password" }; // User credentials
const comparisonData = { username: "example_user", password: "hashed_password_here" }; // User data from the database
const jwtTokens = await auth.verifyWithPassword(originalData, comparisonData, RETURN_TYPE.JWT.TOKEN);
//or
const resultBasic = await auth.verifyWithPassword(originData, comparisonData) // return true or false
```

### 4. Generating One-Time Password (OTP)

Suppose you want to generate a one-time password for MFA verification. You can do it as follows:

```javascript
const secretKey = "user_secret_key_here"; // Secret key for MFA
const oneTimePassword = await auth.generateOTP(secretKey);
```

## Conclusion

This guide demonstrated how to use the authentication system in a Node.js application with a specific example. Make sure to handle errors appropriately and customize the code according to your application's requirements.
```