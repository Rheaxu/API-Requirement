

# API Requirements


## Token (included in HTTPHeader ) 
Why? When user login, the server generates a token(JWT) for the user. The token is time sensitive. It will expire after a certain amount of time. Every http request will be sent along with the token. When the user open the app again, token validation will be intriggered. If the token is still valid, the user will be automatically logged in, otherwise, the user will be asked to login again.   
 
__Example:__  
Add a middleware to all api's that all the requests will firstly stop here for server to check the validation of token. If the token is valid then next() to the designated api, otherwise reject this request.
  
In server.js:
	
	/* the first two api will not go through the middleware */
	apiRoutes.post('/authenticate', function(req, res) {
		//...
	});
	apiRoutes.post('/register', function(req, res) {
		//...
	});
	
	/* route middleware to verify a token */
	apiRoutes.use(function(req, res, next) {
		var token = req.body.token || req.query.token || req.headers['x-access-token'];
		if (token) {
    		jwt.verify(token, app.get('superSecret'), function(err, decoded) {
    			if (err) {
     	 			return res.status(403).send({
            		success: false,
            		message: 'Log in expired.'
        			});
      			} else {
        			// if everything is good, save to request for use in other routes
        			req.decoded = decoded;
        			next();
      			}
      		});
    	} else {
    		// if there is no token
    		// return an error
    		return res.status(403).send({
        		success: false,
        		message: 'Log in expired.'
    		});
    	}
    });
	
	/* apply the routes to our application with the prefix /api */
	app.use('/api', apiRoutes); // apply the routes to our application with the prefix /api
	
In config.js:

	module.exports = {
    	'secret': 'whosyourdaddy',
    	'database': 'mongodb://noder:noderauth&54;proximus.modulusmongo.net:27017/so9pojyN'
	};

### API 1: Change password
__/password__    
**HttpHeader:**'x-access-token': token   
**Content-Type:**'application/json'  
**Request Body:** userid, old_pwd, newpwd  
**Response:** err_msg (if the old_pwd is not correct or changing new password fails)


###API 2: Logout
__/logout__  
The server will delete the token from the database  
**HttpHeader:**'x-access-token': token   
**Content-Type:** "application/json"  
**Reqeust Body:** email  


###API 3: Forget Password
__/password__  
User provide his/her email, server will generate a random password and sent to the user through email  
**HttpHeader:**'x-access-token': token  
**Content-Type:** "application/json"  
**Request Body:** email  
**Response Body:** success/err_msg  


###API 4: Profile 
// avatar might be too large to upload every time?  
__/profile__  
Only the following fields can be changed through this method: firstName, lastName, email, gender, birthday, avatar, phone, other. Password has to be changed in forget password. Email cannot be changed once register is finished.  
**HttpHeader:**'x-access-token': token   
**Content-Type:** "application/json"  
**Request Body:** firstName, lastName, email, gender, birthday, avatar, phone, other  
**Content-Type:** "application/json"  


###API 5: Login
__/login__  
The user will get a token the first time it logs in. When the user reopen the app, the token will be validated first. If it's still valid, the user will be automatically logged in, otherwise, the user will be asked to enter login info again  
**HttpHeader:**'x-access-token': token  
**Content-Type:** "application/json"  
**Request Body:** {username: '', password:''}  
**Response Body:** userInfo: {username: '', token: '', firstName:'', lastName:'', gender: '', birthday: '', avatar: '', phone: '', other: ''}


###API 6: Sign Up
__/signup__  
**HttpHeader:**'x-access-token': token  
**Content-Type:** "application/json"  
**Request Body:** userInfo: {username: '', token: '', firstName:'', lastName:'', gender: '', birthday: '', avatar: '', phone: '', other: ''}  
Avatar, phone, other are not required. Other fields are required.


###API 7: Avatar
__/avatar  
The server will upload the profile pic to s3 storage  
**HttpHeader:**'x-access-token': token  
**Content-Type:** "application/json"   
**Request Body:** the binary data of the picture  
**Response Body:** avatar: {success/err_msg, pic url}  

