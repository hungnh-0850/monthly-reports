Ở một bài viết trước, mình đã từng viết 1 hướng dẫn customs server của ứng dụng Nextjs sử dụng FW Express của Nodejs để tiện
hơn trong quá trình tùy chỉnh, phát triển sau này.
https://viblo.asia/p/custom-server-express-cho-ung-dung-nextjs-gGJ599oa5X2

Và tiếp nối chủ đề đó, bài viết hôm nay mình sẽ hướng dẫn mọi người viết 1 ứng dụng Realtime nhở sử dụng Socket.Io trên Nextjs
Chỉ là 1 ứng dụng nhỏ đó là việc bắn Notification đến 1 User bất kỳ.

## Đây sẽ là hình ảnh ví dụ sản phẩm
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
