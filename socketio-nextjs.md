Ở một bài viết trước, mình đã từng viết 1 hướng dẫn customs server của ứng dụng Nextjs sử dụng FW Express của Nodejs để tiện
hơn trong quá trình tùy chỉnh, phát triển sau này.
https://viblo.asia/p/custom-server-express-cho-ung-dung-nextjs-gGJ599oa5X2

Và tiếp nối chủ đề đó, bài viết hôm nay mình sẽ hướng dẫn mọi người viết 1 ứng dụng Realtime nhở sử dụng Socket.Io trên Nextjs
Chỉ là 1 ứng dụng nhỏ đó là việc bắn Notification đến 1 User bất kỳ.

### Đây sẽ là hình ảnh ví dụ sản phẩm
![S](https://i.imgur.com/DPBVBkA.gif)

## Bước 1: Add socket.io phía sever

Mình sẽ coppy phần code Custom Server Express cho Nextjs ở bài trước. Đã có cài đặt và require socket.io

```sh
const app = require('express')()
const server = require('http').Server(app)
const io = require('socket.io')(server)
const next = require('next')

const port = parseInt(process.env.PORT, 10) || 3000
const dev = process.env.NODE_ENV !== 'production'
const nextApp = next({ dev })
const nextHandler = nextApp.getRequestHandler()

// Phần code xử lý Socket.io .on và .emit mình sẽ viết tại đây

nextApp.prepare().then(() => {
  app.get('/messages/:chat', (req, res) => {
    res.json(messages[req.params.chat])
  })

  app.get('*', (req, res) => {
    return nextHandler(req, res)
  })

  server.listen(port, err => {
    if (err) throw err
    console.log(`> Ready on http://localhost:${port}`)
  })
})
```
## Bước 2. Add socket.io-client phía frontend Nextjs
Bây giờ đến lượt add socket.io-client đến phía frontend Nextjs. Các bạn thêm package này vào file package.jon
    "socket.io-client": "^2.2.0"
Vì ứng dụng Notification này mong muốn lắng nghe để nhận data mới được bắn về ở mọi trang trogn website. Nên mình luôn cần duy trì một kết nối đến socketio. Do đó mà mình sẽ đặt phần code lắng nghe này ở file app.js của Nextjs

Mình sẽ custom 1 Hook dành cho việc đăng ký room (Giống như channel bên ActionCable của Rails). Mục đích của mình ở đây chính là mỗi User khi login ở web sẽ được join vào 1 room riêng của họ. 
Và mỗi lần có Notification người nhận là ai thì mình sẽ bắn vào cái room id tương ứng này. Cái room mình sẽ đặt tên chứa ID của current_user để bảo đảm nó luôn là duy nhất với mỗi User.

- Sử dụng userEffect Hook,file useSocketNotification.js như sau.


```sh
import { useEffect } from 'react'
import io from 'socket.io-client'

//Kết nối đến socket.io
const socket = io()

export default function useSocketNotification(data, cb) {
  useEffect(() => {
    if (data.user.currentUser) {
      // Nếu User đã login vào hệ thống. Mình sẽ emit một cái room có tên là: room{user_id}
      // Ví dụ, user có ID một sẽ được tạo 1 cái room có room1. Sau này mình cần bắn riêng cái gì cho user1 thì sẽ bắn vào cái room1 này (Còn cách tạo roomvà add user vào như thế nào mình sẽ
      socket.emit('registerRoom', { id: `room${data.user.id}` });
      socket.on(data.eventName, cb)
    }

    return function useSocketNotificationCleanup() {
      socket.off(data.eventName, cb)
    }
  }, [data.eventName, cb])

  return socket
}
```

Phía code server. Mình cũng sẽ phải lắng nghe cái tên room đã được emit trên kia để tạo room tương ứng. 
Cách tạo như thế nào thì các bạn có thể đọc kỹ document ở trang chủ của socket.io nhé
https://socket.io/docs/rooms-and-namespaces/


-  Code phía server.js

```sh
//Lắng nghe các kết nối
io.on('connection', socket => {
  // Dang ky room (Channel de lang nghe)
  socket.on('registerRoom', (room) => {
   // Đây chính là cái room id tương ứng khi đã emit ở phần code frontend trên kía. Nó sẽ tạo room.
   // Sau đó add socket đó vào room luôn. Mỗi kết nối tương ứng 1 socket.
    socket.join(room.id);
  })
})
```

- File server.js tạm thời lúc này của mình

```sh
const app = require('express')()
const server = require('http').Server(app)
const io = require('socket.io')(server)
const next = require('next')

const port = parseInt(process.env.PORT, 10) || 3000
const dev = process.env.NODE_ENV !== 'production'
const nextApp = next({ dev })
const nextHandler = nextApp.getRequestHandler()

// Phần code xử lý Socket.io .on và .emit mình sẽ viết tại đây
io.on('connection', socket => {
  // Dang ky room (Channel de lang nghe)
  socket.on('registerRoom', (room) => {
   // Đây chính là cái room id tương ứng khi đã emit ở phần code frontend trên kía. Nó sẽ tạo room.
   // Sau đó add socket đó vào room luôn. Mỗi kết nối tương ứng 1 socket.
    socket.join(room.id);
  })
})


nextApp.prepare().then(() => {
  app.get('/messages/:chat', (req, res) => {
    res.json(messages[req.params.chat])
  })

  app.get('*', (req, res) => {
    return nextHandler(req, res)
  })

  server.listen(port, err => {
    if (err) throw err
    console.log(`> Ready on http://localhost:${port}`)
  })
})
```
