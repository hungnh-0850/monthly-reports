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
      // Ví dụ, user có ID một sẽ được tạo 1 cái room có room1. Sau này mình cần bắn riêng cái gì cho user1 thì sẽ bắn vào cái room1 này (Còn cách tạo room và add user vào như thế nào mình sẽ viết ở file server.js
      // Tên event cho việc đăng ký room mình sẽ đặt tên là registerRoom
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
## Bước 3: Viết code để gửi và nhận dữ liệu realtime thông báo khi có comment mới

Khi mỗi user giờ đã có 1 room riêng của nó cho việc giao tiếp (emit - phát ra và on - lắng nghe). Tiếp theo mình sẽ xử lý để cho nó bắn data về khi có thông báo mới.
Ở ví dụ này, mình muốn khi có bình luận mới thì sẽ gửi thông báo notification đến người được reply. Do đó, mình sẽ viết code ở phần liên quan đến comment.

- Mình đặt tên event là pushNotification

Khi tạo comment thành công. Mình sẽ được đối tượng sẽ nhận notification.
Mình sẽ gửi lên phía server một event có tên pushNotification. Chứa id của notification. Mục đích là từ notifcation id này mình sẽ find để nhận được object comment, sau đó mình sẽ lấy được ID của user người nhận và nội dung comment để bắn về cho đúng người nhận. Lý do là nếu bắn trực tiếp ID người nhận và content từ frontend thì dễ bị hack khi họ fetch nội dung, ID người nhận

```sh
import io from 'socket.io-client'
const socket = io()
  const data = {
    id: this.props.createComment.notiId,
    token: this.props.token
  }
socket.emit('pushNotification', data)
```
- Code phía file server.js mình sẽ lắng nghe cái event pushNotification đó cùng với cái id notification lúc gửi lên.
Sau đó mình gọi đến API phía backend (Ở đây mình dùng rails) để lấy được response data_res có chứa nội dung notification mình cần bắn về cho người nhận

Sau đó mình sẽ dựa vào cái ID người nhận receiver_id đó để bắng data về tương ứng cái room này.
Code cụ thể sẽ như sau

```sh
io.on('connection', socket => {
  // Dang ky room (Channel de lang nghe)
  socket.on('registerRoom', (room) => {
    socket.join(room.id);
  })

  socket.on('pushNotification', (data) => {
    const options = {
      url: `http://localhost:3001/api/notifications/${data.id}/send_noti`,
      headers: {
        Authorization: `Bearer ${data.token}`,
      }
    };
    request(options, function callback(error, response, body) {
      if (!error && response.statusCode == 200) {
        const data_res = JSON.parse(body)
        io.to(`room${data_res.receiver_id}`).emit('pushNotification', {
          value: data_res.data
        });
      }
    });
  })
})
```
- Tiếp theo, phía Nextjs frontend. Mình chỉ cần require cái Hook ban đầu để lắng nghe data trả về.
Sau đó set lại số unreadnotifcation và content tương ứng

```sh
  useSocketNotification({
    eventName: 'pushNotification',
    user: login,
  }, (notifications) => {
    handleNotification(notifications)
  })
```

HhandleNotification mình sẽ viết để cập nhật lại state cho việc hiển thị notifcation ở trên.


```sh
const handleNotification = (notifications) => {
    setNotifications({
      notification: notifications.value.notifications.data,
      hasMore: notifications.value.has_more,
      requesting: false,
      unread: notifications.value.count_unread_notifications,
      errors: false,
    })
```

Trong socket.io , sử dụng .emit có nhiều option để bắn dữ liệu đến một đối tượng.Ở trên mình cần bắn đến 1 đối tượng duy nhất. cụ thể nên mình cần tạo cái room tương ứng cho chúng. 
Có rất nhiều lệnh với .emit mà các bạn có thể tham khảo

https://viblo.asia/p/mot-so-dieu-can-biet-khi-su-dung-socketio-Ljy5VoayKra

```sh
  socket.emit('hello', 'can you hear me?', 1, 2, 'abc');

  // Gửi cho tất cả client ngoại trừ người gửi
  socket.broadcast.emit('broadcast', 'hello friends!');

  // Gửi cho tất cả client trong room 'game' ngoại trừ người gửi
  socket.to('game').emit('nice game', "let's play a game");

  // Gửi cho tất cả client trong room 'game1' và room 'game2' ngoại trừ người gửi
  socket.to('game1').to('game2').emit('nice game', "let's play a game (too)");
```


Vậy là mình đã kết thúc một bài hươngs dẫn nhỏ giúp các bạn sử dụng Socket.Io trên Nextjs. Hi vọng với cơ sở này, các bạn có thể ứng dụng để xử lý nhiều hơn cho trang web của bạn.
