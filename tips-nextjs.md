
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

Ngoài ra, đã có 1 dev phát triển tiện ích giúp cho việc genrate sitemap ứng dụng của bạn. Nó sẽ đọc theo cấu trúc Routing ở pages để tạo ra cấu trúc với các URL ứng dụng của bạn đang có.
https://github.com/IlusionDev/nextjs-sitemap-generator

Sau đó, hãy chủ động gửi sitemap của bạn đến các công cụ tìm kiếm thông qua các dường dẫn sao.

- Với Google

http://www.google.com/ping?sitemap=<complete_url_of_sitemap>
Ex: http://www.google.com/webmasters/sitemaps/ping?sitemap=http://www.example.com/sitemap-file.xml

- Với yahoo:
http://search.yahooapis.com/SiteExplorerService/V1/updateNotification?appid=YahooDemo&url=http://www.domain.com/sitemap.xml

- Với Ping
http://www.bing.com/webmaster/ping.aspx?siteMap=[your sitemap web address]

# 4. Thêm các thẻ metadata đầy đủ chủ website của bạn
Các thể metadata ( meta tag, openGraph) sẽ giúp các công cụ tìm kiếm (crawlers) hiểu và index các nội dung trên các trang của bạn một cách chính xác nhất.
Mặc dù, Nextjs đã tự đông thêm 1 số thẻ meta data cơ bản. Bao gồm cả the viewport và content type.
Tuy nhiên, bạn cần cài đặt 1 số thẻ meta quan trọng cần có khác như meta desciption tag hay title tag thông qua việc chèn vào header tương ứng của các pages.

Và để có thể cấu hình 1 cách đầy đủ và chính xác, dễ dàng nhất cho từng URL và trang web. Mình khuyên các bạn sử dụng 
https://github.com/garmeeh/next-seo

Sau đó, các bạn chèn đoạn mã này vào file _app.js. 
```sh
import App, { Container } from 'next/app';
import React from 'react';
import { DefaultSeo } from 'next-seo';

export default class MyApp extends App {
  render() {
    const { Component, pageProps } = this.props;
    return (
      <Container>
        <DefaultSeo
          title="WSM"
          description="WSM - the timesheet management application ia now ready to appear on mobile phones to help employees manage their check-in, check-out times, time-offs as well as OT times"
          canonical="https://wsm.vn"
          openGraph={{
            type: 'website',
            locale: 'vi',
            url: 'https://wsm.vn',
            site_name: 'WSM',
          }}
          twitter={{
            handle: '@handle',
            site: '@site',
            cardType: 'summary_large_image',
          }}
        />
        <Component {...pageProps} />
      </Container>
    );
  }
}
```

Nó sẽ giúp trang web của bạn mặc định có các thẻ metatag này xuất hiện ở mọi URL trong web của bạn.
Nếu muốn 1 trang cụ thể nào đó có các thẻ meta tag khác với các meta tag mặc định ở trền thì bạn chỉ cần ghi đè phần cấu hình riêng cho từng trang đó thông qua thẻ <NextSeo>

```sh
import React from 'react';
import { NextSeo } from 'next-seo';

export default () => (
  <>
    <NextSeo
      title="Manage requests"
      description="Manage requests for leader"
      canonical="https://wsm.vn/requests"
      openGraph={{
        url: 'https://wsm.vn/requests',
        title: 'Manage requests',
        description: 'Manage requests for leader',
        images: [
          {
            url: 'https://www.example.ie/og-image-01.jpg',
            width: 800,
            height: 600,
            alt: 'Og Image Alt',
          },
          {
            url: 'https://www.example.ie/og-image-02.jpg',
            width: 900,
            height: 800,
            alt: 'Og Image Alt Second',
          },
          { url: 'https://www.example.ie/og-image-03.jpg' },
          { url: 'https://www.example.ie/og-image-04.jpg' },
        ],
        site_name: 'SiteName',
      }}
      twitter={{
        handle: '@handle',
        site: '@site',
        cardType: 'summary_large_image',
      }}
    />
    <p>Manage request</p>
  </>
);
```

The end!

