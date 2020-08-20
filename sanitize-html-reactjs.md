Khi làm việc với reactjs, chắc hẳn bạn đã đôi lần muốn hiển thị một nội dung với các thể html. Ví dụ như content của 1 bài viết khi soạn thảo bằng Editor,
hay 1 content của comment có các thẻ ảnh, chèn link trong đó. Bạn quan ngại nếu sử dụng ``dangerouslySetInnerHTML`` trong reactjs thì quá nguy hiểm. 
sẽ dễ dàng bị hack với lỗ hổng XSS phổ biến mà nhiều website mắc phải hiện nay.

Tuy nhiên, việc render các nội dung html này là cần thiết. Vậy để hiển thị chung 1 cách an toàn, sẽ phải làm sao đây?
Bài viết hôm nay sẽ là câu trả lời cho bạn.
