API Documentation
========================

###API 1: Change password(PUT)
__/password__    
**HttpHeader:** "Authorization": token   
**Content-Type:** "application/json"  
**Request Body:**  
```json 
	{
      "oldPwd": "oldPassword",
      "newPwd: "newPassword"
    }
```

###API 2: Logout(DELETE)
__/logout__  
The server will delete the token from the database  
**HttpHeader:** "Authorization": token   
**Content-Type:** "application/json"   


###API 3: Forget Password(TODO)
__/password__  
**HttpHeader:** "Authorization": token  
**Content-Type:** "application/json"   


###API 4: Change Profile(PUT)
__/profile__  
Only the following fields can be changed through this method: firstName, lastName, gender, birthday, avatar, phone, other. Password has to be changed in forget password. Email cannot be changed once register is finished.  
**HttpHeader:**"Authorization": token   
**Content-Type:** "application/json"  
**Request Body:**  
```json
	  {
		"firstName": "newFirstName",
		"lastName: "newLastName",
		"gender": "f",
		"birthday": "2014/11/22",
		"avatar": "",
		"phone": "6178169142",
		"other": "somethingElse"
	  }
```
###API 5: Login(POST)
__/login__  
The user will get a token the first time it logs in. When the user reopen the app, the token will be validated first. If it's still valid, the user will be automatically logged in, otherwise, the user will be asked to enter login info again    
**Content-Type:** "application/json"  
**Request Body:**  
```json
	{
      "email": "troychen@bu.edu",
      "password: "atLeastSixDigit"
    }
```
###API 6: Sign Up(POST)
__/signup__   
**Content-Type:** "application/json"  
**Request Body:**  
```json
	{
		"firstName": "xuanyi",
		"lastName: "chen",
		"gender": "m",
		"birthday": "2014/11/22",
		"avatar": "",
		"phone": "6178169142",
		"other": ""
	}
```
Avatar, gender, other are not required. Other fields are required.  

###API 7: Avatar(PUT)
__/avatar__  
The server will upload the profile pic to s3 storage  
**HttpHeader:** "Authorization": token  
**Content-Type:** "application/json"   
**Request Body:** the binary data of the picture  


###API 8: Get Profile(GET)
__/profile__
Before the user tries to update the profile, this api will provide the information stored in the database for reference.  
**HttpHeader:** "Authorization": token  
**Content-Type:** "application/json"   


##General Response Format
```json
    response:
    {
      "request": {
        "headers": {
          "host": "localhost:8081",
          "connection": "keep-alive",
          "user-agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.84 Safari/537.36",
          "postman-token": "e1e22fc0-279f-23ba-1b33-5a6018f34201",
          "cache-control": "no-cache",
          "authorization": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InRyb3ljaGVuMUBidS5lZHUiLCJpYXQiOjE0NjU4NTE2MTAsImV4cCI6MTQ2NTg1NTIxMH0.cGGhW-rnmZCrcv70vqDWGgSYhk7ZyZzwDi_RkQCe9F4",
          "content-type": "application/json",
          "accept": "*/*",
          "accept-encoding": "gzip, deflate, sdch",
          "accept-language": "en-US,en;q=0.8"
        },
        "method": "GET",
        "url": "/profile",
        "status": 200,
        "body": {},
        "requestTime": "2016-06-13T21:06:12.069Z"
      },
      "data": [
        {
          "firstName": "xuanyi",
          "lastName": "chen",
          "gender": "m",
          "birthday": "2014-11-22T05:00:00.000Z",
          "avatar": null,
          "phone": "6178169142",
          "other": null
        }
      ],
      "error": {
        "message": ""
      },
      "responseTime": "2016-06-13T21:06:12.070Z"
    }
```  
  </br>
  </br>
1. The response.request.authorization, response.request.body, response.data, response.err.message may be empty depending on HTTP methods and err.  
2. If an error occures, the response.error.message is sure to be "not null", and the response.status is not 200.  
3. Success response will not return response.err.message, and the response.status is 200.  
4. Anything relating to the password is hided in response.  

__http_method_and_response_body__  
**GET**: response.request.body is empty, response.data is the data retrived from the database  
**POST**: response.request.body is the new data intended to insert into database, response.data is empty  
**PUT**: response.request.body is the new data intended to update database, response.data is empty  
**DELETE**: response.request.body is empty, response.data is empty

If error occures, the response.request.body will be the req.body if any, response.data is empty

###Token (included in HTTPHeader.Authorization ) 

__decoded_jwt_sample__
```json
    {
      "header": {
        "alg": "HS256",
          "typ": "JWT"
      },
      "payload: { 
        "email": 'troychen1@bu.edu', 
        "iat": "1465433434",
        "exp": "1465437034" 
      },
      "signature":""
    }
```
The jwt is encoded with HS256. The signature part is generated automatically.

### DATATYPE(Signup, Change profile)

__datatype_and_requirement__

**password:** should be at least 6 digits, do not encode;  
**firstName:** should be less than 100 characters;  
**lastName:** should be less than 100 characters;  
**email:** should be a good formatted email, eg, hehechen@bu.edu;  
**gender:** should be single character, lower case 'm' or 'f';  
**birthday:** should be like 1991/11/12;  
**avatar:** should be binary code, max 65,535 bytes;  
**phone:** should be numbers only, more than 6 digits, max 20 digits;  
**other:** should be text, max length 65,535 characters  

