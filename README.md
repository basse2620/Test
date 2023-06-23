const app = require('express')();
const httpServer = require('http').createServer(app);
const io = require('socket.io')(httpServer, {
  cors: { origin: '*' }
});

const ipAddress = '192.168.20.33';
const port = process.env.PORT || 83;

const rooms = new Map(); // Map to store the rooms

io.on('connection', (socket) => {
  console.log('A user connected');

  socket.on('joinRoom', (roomId) => {
    socket.join(roomId);
    console.log(`User joined room ${roomId}`);
  });

  socket.on('leaveRoom', (roomId) => {
    socket.leave(roomId);
    console.log(`User left room ${roomId}`);
  });

  socket.on('message', (message) => {
    console.log(message);
    io.to(message.roomId).emit('message', message.content);
  });

  socket.on('disconnect', () => {
    console.log('A user disconnected');
  });
});

httpServer.listen(port, ipAddress, () => console.log(`Listening on port ${port}`));
