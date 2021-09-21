
Thông thường, khi có 1 template html,css đẹp. Bạn muốn apply vào ứng dụng Rails của bạn? Tuy nhiên nhiều bạn có thể sẽ gặp khó khăn do cấu trúc của Rails khá khác với những FW có thể bạn đã tiếp xúc trước đây như Laravel (PHP) như bị 404 images, css, js hay fonts.
Bởi với Rails, các folder đều không thể truy cập được qua đường dẫn (Ngoại trừ folder public).

Bài viết này sẽ hướng dẫn các bạn 2 cách để có thể implement 1 template đẹp nào đó vào ứng dụng rails của bạn.

## Cách 1: Ném hết css,js, fonts, images vào folder public

Đây có lẽ là cách đơn giản nhất bạn có thể thực hiện. Sau khi có folder template. Bạn có thể ném chúng hết vào folder public. Sau đó chỉ cần chèn script js, 

VD: Mình có folder template có cấu trúc như sau

```
template_shop/js/main.js
template_shop/js/dashboard.js
template_shop/css/main.css
template_shop/css/style.css
template_shop/img/logo.png
template_shop/fonts/MaterialIcons-Regular.ttf
```

Mình sẽ ném nguyên folder template_shop vào folder public của rails. Sau đó mình chỉ cần nhúng css, js vào header của rails app thông qua file application.erb.html như sau

```
<link rel="stylesheet" media="all" href="/template_shop/css/main.css" />
<script src="/template_shop/css/main.js"></script>
```

Khi đó các file css nếu có có gọi file images, fonts thì sẽ gọi ở dạng đường dẫn tương đối. Nếu đã đặt đúng cấu trúc như trên ở folder public thì sẽ tự động được loading đúng.

Cách này nhanh tuy nhiên có một nhược điểm về cấu trúc dự án và có thể bị cache css, js.
Đầu tiên về cấu trúc, là không nên đặt bất cứ thứ gì vào folder public. Đây là folder mà sau khi Rails đã complie css, js, images..vv từ app/assets , app/vender hay các folder,file khác được define ở **config/initializers/assets.rb** sẽ được rails move vào đây. Cũng như thay thế các link trỏ đến chúng thông qua các helper url, kèm theo version sau mỗi lần compile. 
Mục đích thứ 2 là không để bị cache css, js sau mỗi lần có thay đổi code.

Do đó, chúng ta sẽ cần tới cách 2

## Cách 2: Tổ chức code để complie

Đây là cách hoàn hảo và hợp lý nhất để áp dụng 1 template đã được design, có sẵn css, js trước đó. Code sẽ được tổ chức theo rails
Với folder như trên. Chúng ta sẽ move js đến folder app/assets/stylesheets 
Ở đây mình sẽ tạo folder có tên new_template. Move 2 file main.js và dashboard.js vào.

Để được cây thư mục như sau
app/assets/javascripts/new_tempalte/dashboad.js
app/assets/javascripts/new_tempalte/main.js

Vào file application.js và requre nó vào
```
//= require new_tempalte
```
Tương tự cho css.
app/assets/stylesheets/new_tempalte/main.css
app/assets/stylesheets/new_tempalte/style.css

Mở
app/assets/stylesheets/application.scss
Và thêm

```
 *= require new_tempalte
```

Lúc này các file css, js ở trên sẽ được complie, sau đó được move qua public. Rails cũng sẽ tự động load đúng URL chèn vào header nhờ 2 thẻ này ở application.html.erb

```
<%= stylesheet_link_tag "application", media: "all" %>
<%= javascript_include_tag "application" %>
```

Tuy nhiên, nó vẫn chưa thể nào hoạt động trơn tru bởi nếu các file css có gọi đến các file fonts thì rails sẽ không load được chúng. Do đó chũng ta cũng sẽ cần phải sửa ở chỗ này. Cho phép rails complie cũng như quản lý fonts bằng cách sau đây

Tạo thêm folder fonts vào app/assets/fonts . Move hết các file font qua đây. Sau đó mở file ` config/initializers/assets.rb` thêm dòng `Rails.application.config.assets.paths << Rails.root.join("app", "assets", "fonts")`

Tiếp đến mở các file css, sass sửa phần url của fonts thành như sau

```
@font-face {
  font-family: 'Material Icons';
  src: url(font-path('MaterialIcons-Regular.ttf'));
}
```

Như vậy là xem như đã hoàn chỉnh việc apply 1 template vào ứng dụng rails.
Bài viết này cũng giúp bạn biết cách config để complie thêm các resource cần thiết trong ứng dụng rails.
