
Thông thường, khi có 1 template html,css đẹp. Bạn muốn apply vào ứng dụng Rails của bạn? Tuy nhiên nhiều bạn có thể sẽ gặp khó khăn do cấu trúc của Rails khá khác với những FW có thể bạn đã tiếp xúc trước đây như Laravel (PHP) như bị 404 images, css, js hay fonts.
Bởi với Rails, các folder đều không thể truy cập được qua đường dẫn (Ngoại trừ folder public).

Bài viết này sẽ hướng dẫn các bạn 2 cách để có thể implement 1 template đẹp nào đó vào ứng dụng rails của bạn.


Cách 1: Ném hết css,js, fonts, images vào folder public

Đây có lẽ là cách đơn giản nhất bạn có thể thực hiện. Sau khi có folder template. Bạn có thể ném chúng hết vào folder public. Sau đó chỉ cần chèn script js, 

VD: Mình có folder template có cấu trúc như sau

template_shop/js/main.js
template_shop/css/main.css
template_shop/img/logo.png
template_shop/font/acb.tf

Mình sẽ ném nguyên folder template_shop vào folder public của rails. Sau đó mình chỉ cần nhúng css, js vào header của rails app thông qua file application.erb.html như sau

<link rel="stylesheet" media="all" href="/template_shop/css/main.css" />

<script src="/template_shop/css/main.js"></script>

Khi đó các file css nếu có có gọi file images, fonts thì sẽ gọi ở dạng đường dẫn tương đối. Nếu đã đặt đúng cấu trúc như trên ở folder public thì sẽ tự động được loading đúng.

Cách này nhanh tuy nhiên có một nhược điểm về cấu trúc dự án và có thể bị cache css, js.
Đầu tiên về cấu trúc, là không nên đặt bất cứ thứ gì vào folder public. Đây là folder mà sau khi Rails đã complie css, js, images..vv từ app/assets , app/vender hay các folder,file khác được define ở applicatin assets sẽ được rails move vào đây. Cũng như thay thế các link trỏ đến chúng thông qua các helper url, kèm theo version sau mỗi lần compile. 
Mục đích thứ 2 là không để bị cache css, js sau mỗi lần có thay đổi code.

Do đó, chúng ta sẽ cần tới cách 2

Cách 2: 
