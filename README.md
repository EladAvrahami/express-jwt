# express-jwt
express+jwt 

### .env
<pre>
ACCESS_TOKEN_SECRET=1f10c361f1ede14475cdf00f44ab3febaf527f8626e4c4f1a4ccd276a8b93e5cf87f9e49ad2bf94915cb37d43d7c258fd58bf067b0b28395699a7447b522a27b
REFRESH_TOKEN_SECRET=16f4ac6c0ce9bfa5cd9ad6c9fcbfc6dc8dae2a6f2ad8bf18eb0ae937e652d394eee3e340bef5747179003b60c6ff3fdb232e6d872019d4174f6a00ebb01ef5b4

#require('crypto').randomBytes(64).toString('hex')
</pre>

### server.js
<pre>
//https://www.youtube.com/watch?v=mbsmsi7l3r4&list=RDCMUCFbNIlppjAuEX4znoulh0Cw&start_radio=1&rv=mbsmsi7l3r4&t=79  6
require('dotenv').config()
const express =require("express");
const app=express()
const jwt =require("jsonwebtoken")

//help server send data in json format
app.use(express.json())

const posts=[
    {
       username:'elad',
       title:'post 1' 
    },
    {
        username:'jim',
        title:'post 2' 
     }
]

//"Get posts req method"
app.get('/posts',authenticateToken, (req,res)=>{
   //req.user- access user after doing middleware


    res.json(posts.filter(post => post.username===req.user.name))//bring back only the obj that have the same name insted of all "posts" array (in real better id )
})




//every time i make req to login if username authenticated correctly this req make a new token
app.post('/login', (req,res)=>{
    //authenticate user

     //get user name 
    const username=req.body.username

    //equalles to the value of the array json that i create above
    const user={ name: username}

    //jwt contain user details and secret key i define in env file to create new sign
    const accessToken = jwt.sign(user, process.env.ACCESS_TOKEN_SECRET)
    //res wiil be jwt formatted to json 
    res.json({accessToken: accessToken })
})



//creat a middleware that wiil be used in the "Get posts req" above
//1get the token that sends to me 
//2verify that its the correct user
//3 return that user to "Get posts req" 
function authenticateToken(req,res,next){
    //the token is going to come from the headerof the req
    const authHeader=req.headers['authorization']
    //use split becous it is a space between the Bearer and the token👇👇    =>Bearer TOKEN
    //"authHeader &&authHeader"-👇it means if i have a autheader so return the token you found after split it
    const token =authHeader &&authHeader.split(' ')[1]//get the sec param from the array that contain Bearer and the TOKEN
    //in the post man the token wiil be after the keyword "Bearer" word 

    if (token ==null) return res.sendStatuse(401)//return not have token if true
    
    //if token exists make this callBack: take user req as json that created above and check validation of token
    jwt.verify(token,process.env.ACCESS_TOKEN_SECRET,(err,user)=>{
        if (err) return res.sendStatuse(403)// token not valid error
        //set my user in the middel of the req to the user full of verified data above
         req.user=user
         //finish middleware and execute next method (in this case "Get posts req method")
         next()
    })
}





app.listen(3000)
console.log('START SERVER ON PORT 3000')
</pre>


### server2.js
<pre>
/* this is copy of the original server i created to demonstrate jwt work in muliti servers with the same secret key * */
//POSTMAN GET http://localhost:4000/posts
 require('dotenv').config()
const express =require("express");
const app=express()
const jwt =require("jsonwebtoken")

//help server send data in json format
app.use(express.json())

const posts=[
    {
       username:'elad',
       title:'post 1' 
    },
    {
        username:'jim',
        title:'post 2' 
     }
]

//"Get posts req method"
app.get('/posts',authenticateToken, (req,res)=>{
   //req.user- access user after doing middleware


    res.json(posts.filter(post => post.username===req.user.name))//bring back only the obj that have the same name insted of all "posts" array (in real better id )
})




//every time i make req to login if username authenticated correctly this req make a new token
app.post('/login', (req,res)=>{
    //authenticate user

     //get user name 
    const username=req.body.username

    //equalles to the value of the array json that i create above
    const user={ name: username}

    //jwt contain user details and secret key i define in env file to create new sign
    const accessToken = jwt.sign(user, process.env.ACCESS_TOKEN_SECRET)
    //res wiil be jwt formatted to json 
    res.json({accessToken: accessToken })
})



//creat a middleware that wiil be used in the "Get posts req" above
//1get the token that sends to me 
//2verify that its the correct user
//3 return that user to "Get posts req" 
function authenticateToken(req,res,next){
    //the token is going to come from the headerof the req
    const authHeader=req.headers['authorization']
    //use split becous it is a space between the Bearer and the token👇👇    =>Bearer TOKEN
    //"authHeader &&authHeader"-👇it means if i have a autheader so return the token you found after split it
    const token =authHeader &&authHeader.split(' ')[1]//get the sec param from the array that contain Bearer and the TOKEN
    //in the post man the token wiil be after the keyword "Bearer" word 

    if (token ==null) return res.sendStatuse(401)//return not have token if true
    
    //if token exists make this callBack: take user req as json that created above and check validation of token
    jwt.verify(token,process.env.ACCESS_TOKEN_SECRET,(err,user)=>{
        if (err) return res.sendStatuse(403)// token not valid error
        //set my user in the middel of the req to the user full of verified data above
         req.user=user
         //finish middleware and execute next method (in this case "Get posts req method")
         next()
    })
}





app.listen(4000)
console.log('START SERVER ON PORT 4000')

</pre>
