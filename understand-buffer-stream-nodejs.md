Chắc hẳn khi làm việc với máy tính hay bất kỳ ngôn ngữ lập trình nào, bạn cũng đã từng nghe qua khái niệm Buffer, Stream, hay binary data đặc biệt là khi học Node.js.
Tuy nhiên, rất ít người trong số chúng ta dành thời gian để tìm hiểu về chúng hoặc hiểu chúng. Hay nói cách khác chúng ta có cảm giác rằng nó không dành cho mình mà chỉ dành cho các chuyên gia Node.js và các nhà phát triển gói mới có thể hiểu được?

Thật sự thì đúng là vậy, nhất là khi bạn chưa từng có nền tảng kiến thức vững trước đây và bắt đầu học Nodejs.

Và khi học Nodejs, bạn sẽ thưởng chỉ tập trung sơ qua kiến thức, sau đó là tiếp cận với các framework để phát triển nhanh các ứng dụng web với các gói Node.js mà không cần hiểu sâu các kiến thức cốt lõi của Node.js và tại sao chúng tồn tại. Bởi vì bạn có thể không bao giờ làm việc trực tiếp với chúng. Điều đó là đúng nếu bạn vẫn đang là 1 developer Nodejs mới vào nghề.

Tuy nhiên, nếu bạn tò mò về Nodejs và muốn tìm hiểu để nâng kiến thức của mình lên một tầm cao mới,  đó là lúc bạn cần tìm hiểu thêm những tính năng cốt lõi của Node.js, chẳng hạn như Buffer.  Và đó chính là nội dung của bài viết này. 
Giúp bạn hiểu sâu hơn về Buffer trong Nodejs nói riêng.

## Hãy cùng mình bắt đầu nhé!

Đầu tiên chúng ta hãy xem qua tài liệu, tài liệu chính thức của Node.js giới thiệu về Buffer.

> Prior to the introduction of TypedArray, the JavaScript language had no mechanism for reading or manipulating streams of binary data. The Buffer class was introduced as part of the Node.js API to enable interaction with octet streams in TCP streams, file system operations, and other contexts.

**Tóm gọn:** Lớp Buffer được giới thiệu như một phần của API Node.js để có thể thao tác hoặc tương tác với các luồng dữ liệu nhị phân.

Nghe có vẻ cũng bắt đầu hình dung ra được. Nhưng… Buffer, streams, binary data… … vẫn còn nhiều kiến thức cần làm rõ. Và chúng ta cùng phân tích rõ hơn.

### Binary data (Dữ liệu nhị phân)

Máy tính lưu trữ và biểu diễn dữ liệu trong các tệp nhị phân. Nhị phân chỉ đơn giản là một tập hợp hoặc một tập hợp các số 1 và 0.
Mỗi số 1 và số 0 trong một tập hợp được gọi là Bit, là một dạng rút gọn của Binary digIT.

Ví dụ:  12 => 1100

Tuy nhiên, không đơn giản chỉ là số. Chung ta có có những dữ liệu phức tạp hơn như hình ảnh và thậm chí cả video. Máy tính biết cách biểu diễn tất cả các loại dữ liệu trong mã nhị phân.

Vậy, làm thế nào một máy tính sẽ biểu diễn chuỗi “L” trong các mã nhị phân? Để lưu trữ bất kỳ ký tự nào trong các mã nhị phân, Đầu tiên Máy tính sẽ chuyển đổi ký tự đó thành một số, sau đó chuyển đổi số đó thành biểu diễn nhị phân của nó.

Đến lúc này, chúng ta biết thêm về bộ ký tự ASCII

![alt](https://upload.wikimedia.org/wikipedia/commons/d/dd/ASCII-Table.svg)


### Character Sets

Character Sets sẽ xác định các quy tắc về số chính xác đại diện cho mỗi ký tự. Chúng ta có các định nghĩa khác nhau về các quy tắc này, những quy tắc rất phổ biến bao gồm Unicode và ASCII. JavaScript thực sự hoạt động tốt với Bộ ký tự Unicode. Trên thực tế, chính Unicode trong trình duyệt của bạn nói rằng 76 phải đại diện cho L. Khi bạn sử dụng câu lệnh `"L".charCodeAt(0)`

Bây giờ, máy tính sẽ biểu diễn số 76 thành biểu diễn nhị phân của nó. Chỉ cần chuyển đổi 76 thành hệ thống cơ số 2 ta được: 
### Character Encoding

Cũng giống như việc có các quy tắc xác định số nào sẽ đại diện cho một ký tự như đã trình bày ở trên, chúng ta cũng cũng có các quy tắc xác định cách số đó nên được biểu diễn thế nào trong các mã nhị phân. Cụ thể là dùng bao nhiêu bit để biểu diễn số. Đây được gọi là Mã hóa ký tự (**Character Encoding**)

Một trong những định nghĩa cho Character Encoding phổ biến là UTF-8. UTF-8 nói rằng các ký tự phải được mã hóa theo byte. Một byte là một tập hợp tám bit - tám bit 1 và 0. Vì vậy, tám số 1 và 0 nên được sử dụng để đại diện cho Điểm mã của bất kỳ ký tự nào trong hệ nhị phân.

Để hiểu điều này, như chúng ta đã đề cập trước đó, biểu diễn nhị phân của số 12 là 1100. Vì vậy, khi UTF-8 nói rằng 12 phải ở 8 bit, UTF-8 đang nói rằng một máy tính cần thêm nhiều bit hơn vào phía bên trái của biểu diễn cơ số 2 thực tế của số 12 để biến nó thành một byte. Vì vậy, 12 nên được lưu trữ là 00001100 (Nó sẽ thêm vào bên trái 4 số 0). 

Do đó, 76 nên được lưu trữ dưới dạng 01001100 với UTF-8.

Bây giờ chúng ta hiểu dữ liệu nhị phân là gì, nhưng các luồng dữ liệu nhị phân từ phần giới thiệu của chúng tôi về bộ đệm là gì?

### Stream trong Nodejs là gì?

Stream trong Node.js chỉ đơn giản là một chuỗi dữ liệu được di chuyển từ điểm này sang điểm khác theo thời gian. Toàn bộ khái niệm bạn có thể hiểu là khi bạn có một lượng lớn dữ liệu cần xử lý, nhưng bạn không cần phải đợi tất cả dữ liệu có sẵn trước khi bắt đầu xử lý.

Về cơ bản, dữ liệu lớn này được chia nhỏ và gửi thành nhiều phần. Vì vậy, từ định nghĩa ban đầu của Buffer (““streams of binary data… in the context of… file system”), điều này đơn giản có nghĩa là dữ liệu nhị phân được di chuyển trong hệ thống tệp. Ví dụ: di chuyển văn bản được lưu trữ trong file1.txt sang file2.txt.
![alt](https://i.ytimg.com/vi/GlybFFMXXmQ/maxresdefault.jpg)

Nhưng chính xác thì buffer giúp chúng ta tương tác hoặc thao tác dữ liệu nhị phân như thế nào trong khi truyền trực tuyến? Chính xác thì bộ đệm này là gì ?

### Buffer trong Nodejs là gì?

Chúng ta đã thấy rằng một luồng dữ liệu (stream of data) là sự di chuyển của dữ liệu từ điểm này sang điểm khác, nhưng chúng được di chuyển như thế nào?

Thông thường, sự di chuyển của dữ liệu thường là với mục đích xử lý hoặc đọc nó và đưa ra quyết định dựa trên nó. Nhưng có một lượng dữ liệu tối thiểu và tối đa mà một quá trình có thể mất theo thời gian. Vì vậy, nếu tốc độ dữ liệu đến nhanh hơn tốc độ quá trình tiêu thụ dữ liệu, thì dữ liệu thừa cần phải đợi ở đâu đó để đến lượt nó được xử lý.

Mặt khác, nếu quá trình sử dụng dữ liệu nhanh hơn dữ liệu đến, thì một số ít dữ liệu đến sớm hơn cần phải đợi một lượng dữ liệu nhất định đến trước khi được gửi đi để xử lý.

Và cái chung ta xem như “Khu vực chờ đợi” chính là vùng đệm (Buffer) ! Đó là một vị trí vật lý nhỏ trong máy tính của bạn, thường là trong RAM, nơi dữ liệu tạm thời được thu thập, chờ đợi và cuối cùng được gửi đi để xử lý trong quá trình phát trực tuyến.

Chúng ta có thể coi toàn bộ luồng (stream) và  buffer process như một trạm xe buýt. Ở một số bến xe, xe buýt không được phép khởi hành cho đến khi có một lượng khách nhất định hoặc đến một giờ khởi hành cụ thể. Ngoài ra, hành khách có thể đến vào những thời điểm khác nhau với tốc độ khác nhau. Cả hành khách và bến xe đều không kiểm soát được việc hành khách đến bến.

Trong mọi trường hợp, những hành khách đến sớm hơn sẽ phải đợi cho đến khi bến xe quyết định gửi xe. Trong khi những hành khách đến khi xe buýt đã tải hoặc khi xe buýt đã khởi hành cần phải đợi chuyến xe tiếp theo.
![alt](https://images.viblo.asia/c6d95cbf-c2bf-4531-add9-72d76cc2e8ab.png)

**Tóm lại**

```
Bộ đệm dữ liệu là một vùng lưu trữ bộ nhớ vật lý được sử dụng để lưu trữ tạm thời dữ liệu trong khi nó đang được di chuyển từ nơi này sang nơi khác.

Bộ đệm có kích thước xác định và giới hạn. Kích thước của bộ đệm được xác định bởi một thuật toán theo từng trường hợp. Bộ đệm là một kỹ thuật được phát triển để ngăn chặn sự tắc nghẽn dữ liệu khi di chuyển từ nơi này đến nơi khác.
```
