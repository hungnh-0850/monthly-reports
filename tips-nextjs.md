
Lý do lớn nhất khiến chúng ta sử dụng Nextjs Framework để phát triển ứng dụng là vì nó hỗ trợ tốt cho SEO, thân thiện với công cụ tìm kiếm.
Tuy nhiên, trong quá trình sử dụng, nếu bạn không biết một số điều sau đây thì quả thataj: Bạn đang bỏ phí những điều tuyệt vời mà Nextjs đang hỗ trợ đó

# 1. Khi chuyển hướng URL, hãy sử dụng Next.js’ Link component

Có 2 cách để phổ biến khi bạn muốn điều hướng người dùng đến 1 URL nào đó trên trang web của bạn.

#### Cách 1. Sử dụng Router.push
```sh
import Router, { withRouter } from 'next/router';
```
Ví dụ
```sh
<Button onClick={Router.push('/post');} />
```

#### Cách 2. Sử dụng <Link> component
```sh
import Link from 'next/link';
```
Ví dụ
```sh
<Link href='/post'><a>Post</a></Link>
```
Ở đây, cách thứ 2 là cách bạn nên ƯU TIÊN DÙNG nếu có thể. Và tránh việc dùng cách 1. Vì Google BOT không thể nhìn thấy LINK bạn muốn chuyển hướng đến nếu như sử dụng cách 1.

Bởi vì trong SEO, có khái niệm được gọi là Link nội bộ (Internal link) giúp điều hướng người dùng và cả BOT đi lại giữa
các nội dung trong website của bạn, nếu google BOT đang ở trang A, có liên kết đến trang B thì khi  crawl dữ liệu, BOT cũng sẽ biết trang B nhờ vào mã HTML ở dạng thẻ a href được 
sinh ra, và BOT sẽ di chuyển qua để  crawl dữ liệu trang B đó.

Cách 2 sẽ giúp BOT dễ dàng biết di chuyển qua lại giữa các trang.

Vì thế, hãy tránh việc sử dụng Next's Router như cách 1. Thay vào đó, hãy luôn sử dụng <Link> component (cách 2) nếu có thế

# 2. Hãy sử dụng URL thân thiện

Trong SEO: 

Một URL với ID: http://domain.com/post/1

Và 1 URL như thế này: http://domain.com/post/toi-yeu-nextjs-toi-yeu-huu-hung

Thì rõ ràng, URL sau là điều bạn cần hướng đến để làm được. Bởi nó sẽ luôn thân thiện hơn với các công cụ tìm kiếm và
tốt cho SEO hơn, là điểm cộng cho Website của bạn vì URL cũng đã thể hiện được được nội dung LINk đó.

Để làm được điều này, lúc phát triển ứng dụng phía backend, bạn nên lưu lại 1 trường để làm URL sau này.

Ví dụ là slug chẳng hạn, render chúng từ title. Và sau này thay vì find_by id: params[:id] thì bạn sẽ find_by slug: params[:slug]
đó.

| ID | title | slug |
| ------ | ------ | ------
| 1 | Tôi yêu Nextjs, tôi yêu hữu hùng| toi-yeu-nextjs-toi-yeu-huu-hung |

Nếu backend bạn đang sử dụng Rails thì có 1 GEM giúp bạn làm điều đó 1 cách dễ dàng chính là
https://github.com/norman/friendly_id

Bạn nên biết và sử dụng nhé!

# 3. Hãy tạo sitemap cho ứng dụng Nextjs của bạn
Nếu bạn chưa biết Sitemap là gì thì nói 1 cách dễ hiểu thì Sitemap chính là bản đồ cho trang web của bạn.
![S](https://seomax.net/wp-content/uploads/2019/07/xml-sitemap-1.png)

Nó có định đạng XML, được các công cụ tìm kiếm sử dụng để thu nhập thông tin của 1 website, để các công cụ đó biết 1 cách nhanh chóng rằng
có những URL nào trên website của bạn để các BOT call thông tin và lập chỉ mục một cách nhanh chóng hơn.

Và khi bạn đã sử dụng Nextjs thì chắc hẳn bạn quan tâm đến SEO. Và câu hỏi là làm thế nào để có thể tạo Sitemap cho ứng dụng
Nextjs. Thật may là trên Internet cũng đã có nhiều bài viết để bạn có thể tham khảo và tạo Sitemap tự động cho ứng dụng của mình

Ngoài ra, đã có 1 dev phát triển tiện ích giúp cho việc genrate sitemap ứng dụng của bạn. Nó sẽ đọc theo cấu trúc Routing ở pages
để tạo ra cấu trúc với các URL ứng dụng của bạn đang có.
https://github.com/IlusionDev/nextjs-sitemap-generator


The end!
