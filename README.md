## Concept

This template is meant to serve as a foundation for every P2/P3 following the React-Express-MySQL stack, as learned in Wild Code School.
It's pre-configured with a set of tools which'll help students produce industry-quality and easier-to-maintain code, while staying as simple as possible to use.

## Setup & Use

### Project Initialization

- In VSCode, install plugins **Prettier - Code formatter** and **ESLint** and configure them
- Clone this repo, enter it
- Run command `npm run setup`
- _NB: To launch the backend server, you'll need an environment file with database credentials. You'll find a template one in `backend/.env.sample`_

### Available Commands

- `setup` : Initialization of frontend and backend, as well as all toolings
- `migrate` : Run the database migration script
- `dev` : Starts both servers (frontend + backend) in one terminal
- `dev-front` : Starts the React frontend server
- `dev-back` : Starts the Express backend server
- `lint` : Runs validation tools, and refuses unclean code (will be executed on every _commit_)
- `fix` : Fixes linter errors (run it if `lint` growls on your code !)

## FAQ

### Tools

- _Concurrently_ : Allows for several commands to run concurrently in the same CLI
- _Husky_ : Allows to execute specific commands that trigger on _git_ events
- _Vite_ : Alternative to _Create-React-App_, packaging less tools for a more fluid experience
- _ESLint_ : "Quality of code" tool, ensures chosen rules will be enforced
- _Prettier_ : "Quality of code" tool as well, focuses on the styleguide
- _ Airbnb Standard_ : One of the most known "standards", even though it's not officially linked to ES/JS
- _Nodemon_ : Allows to restart the server everytime a .js file is udated


## Step to create the chat
Server side :

- 1/ Install the packages socket.io and uniqid
- 2/ Attach socket.io to the server
- 3/ Prepare an initial message in an array
- 4/ Add listeners for connect and disconnect events.

```
io.on('connect', (socket) => {
  console.log('user connected');
  socket.on('disconnect', () => {
    console.log('user disconnected');
  });
});
```


Client side :

- 5/ Install the package socket.io-client
- 6/ When App component mounts (useEffect), create a client side socket connected to the server and set it in a state
```
const socketId = socketIOClient('http://localhost:5000');
```


Server Side :
- 8/ Emit an event with the current message list to the client
```
socket.emit('initialMessageList', messages);
```


Client Side :
- 9/ Listen to the custom event just created on the server, in your useffect

```
socketId.on('initialMessageList', (messages) => {
  // TODO: set the message list in the state
});

```

- 10/ When the new message form is submitted, use the socket in the state to emit an event with the message payload containing the message’s text and author
```
socket.emit('messageFromClient', msg)
```

Great !!! After posting a new message form the client, the server should log the latter
After reloading the client, we should see this new message in the client message list.

Server Side :

- 11/ Once the new message is added to the list, we have to send it back to (all*) the clients with a new emit()


Client Side :

- 12 / Receive the new message sent back from the server and add it to the list. For that, we should use another useEffect (listenning to *messageList,socket*).

```
if (socket) {
  socket.on("newMessage", (message) => {
    setMessageList([...messageList, message]);
  });
}
```

- 12/ We should not forget to purge our component. In the first useEffect, use the return cleaning function to disconnect and turn socket off
```
  socket.emit("disconnectUser", socket.id);
  socket.off();
```


