## Auth / Authorization and Authentication


### Imagine...
We're going on a trip to the amusement park because... rollercoasters are exciting. Being the proactive people we are, we purchased our tickets online ahead of time. We get to the front gate and the ticket agent asks us for proof of identification and the name on the ticket. After confirming you indeed bought a ticket, the agent lets you in and you get to go on every ride your heart desires. Sweet victory.

### ... and we're back
This anology is very similar to the way we can think about user Auth. Are we who we say we are? Are we allowed to do what we're asking to do? Since our user information is stored in our database, it is the responsibilty for our backend to handle Authentication (making sure I am who I say I am). Think of the backend like the ticket agent and your front end as you the client, the attendee. You previously bought a ticket aka you previously signed up as a user and now its time to enter the park/ aka login. It is our responisbility as the attendee to give the agent our proof of ID - our username and password. Let's get started

## Step 1 - Login Form
- Do we have a login form? If we as the client need to provide our username and password to the backend - we need a login form that collects that information
- When the user hits enter/submit, are we sending over ('POST' request to the backend) the username and password? Since the backend is the one doing the authentication, we must send over both pieces of information. Ask them who they are - the username. Confirm they are who they say they are - the password.

##### Okay cool, we've sent our username and password to the backend and all of our infomation checks out - we are autheticated ! What next?
We want to prevent users that are not logged in from seeing the contents of our site. If just anyone could go on the rollercoasters as they pleased, then what's the point of the ticket? So on the frontend how do we confirm someone is logged in?

## Step 2 - Current User
- Does our app know about a current user? If someone is logged in, that means they are authenticated, they exist, and we know who they are. We shoud have a way for a frontend to know who the current user is using the application state. Remember: the username and password collected from the login form are not the current user - just their information
- When are you setting the current user? After the user is authenticated - after we login sucessfully our backend should send back to us the authenticated user object (maybe the username for display purposes and the id that could be used for other requests). It is at this point we set the user in our state

## Step 3 - Protect Components/Routes
- Are you protecting your components/routes? If a user isn't logged in, meaning there is no current user in our state, they shouldn't be able to see the contents of your page. If the user is logged in (meaning we have a current user in state) we should be able to see the page they are requesting to see. Otherwise, redirect to the login component. Also, when you are logged in, you shouldn't be able to see the login view.
- Do multiple routes need to be protected? Instead of using conditional logic in each one of your components that needs to be protected, use a higher order component to run this check for you. It will either return the component the authorized user is trying to view, or if there is no user, it will return the login form

##### CHECKPOINT! You've successfully gotten into the park. You've went on the 2 rollercoasters before realizing that you forgot your cellphone in the car outside. You retreive your phone, but get stopped by the gate attendent upon reentry asking for your ticket and proof of ID... again. But you were just in the park? Well, there is no way to confirm that - there is nothing explicitely saying that you've already completed this process. Try refreshing the page... if all things go according to plan you should be redirected to the login page. How come? Well, a hard refresh means that our local state is getting reset/cleared. We no longer have a current user in state even though we previously logged in. Wouldn't it be great if there was a way to easily reenter the park? Something to confirm that we've done the initial check already? Something like a wristband! Let's continue...

# Persistance
Since we need a current user in our state to be considered logged in, there needs to be a way to persist user information when a user refreshes, closes the window etc kind of like a wristband. Upon initial check of our ID and ticket, we are given a wristband that allows for easy rentry - just scan and go. Welcome, localStorage(our wrist) and JWT (our wristband) - thanks to HTML5, the localStorage object (given to us by web storage), allows us store data locally within the user's browser with no expiration!! Again, *no expiration*. Thanks to our backend, our JWT (JSON WEB TOKEN) is given to us when we successfully login.

## Step 4 - Local Storage
- Are you using storing your JWT in localStorage? We talked about why we use localStorage but why the JWT? We need a way to identify the user that is currently logged in. Just like your wristband, each person has their own. Since localStorage is accessible by the user, it would be unsafe to store something like the plain user ID because the user can change the ID at any moment and disguise themself as a different user. Instead we use a JWT, provided to us by our backend to encrypt user information. We are given the jwt in our response along with the verified user object when we successfully login (when the user is authenticated) and set it in localStorage.
- Are you sending back the JWT to your backend to retrieve the current user upon refresh? Let's think back to why we needed the JWT in the first place. When we refresh the page we lose the currentUser object in state. In order to be logged in, we need that currentUser object. Since user information (such as the id) is encrypted in the JWT, we can send this token back to our server - our server can decode the token, find the associated current user object and send it back.
- When do you send the JWT and what are you doing with the response? You should send the JWT to the backend (GET request) via the request headers when the componentDidMount of the view you're trying to protect. Why a get? We're trying to 'get' the current user object associated to that token. Why the componentDidMount? When your page first loads, you want to be able to check if there is a token in your localStorage so that you can get the user and show them the correct view. If you are trying to protect your whole application, you can use the componentDidMount of your top most component. We get the user object back from our response and set it in our state. 
