# Bài toán lưu dữ liệu tạm thời

Khi làm việc với các ứng dụng Web, cụ thể ở đây trường hợp mình đang sử dụng ngôn ngữ LT Ruby (RoR framework), có một số trường hợp mình cần lưu lại lịch sử, log
của một số tác vụ hành động nào đó, nhưng mình không muốn lưu nó vào database bởi không thật sự cần thiết vì mình chỉ cần có nó trong một thời gian ngắn mà thôi. 

Ví dụ:
Mình muốn lưu lịch sử lỗi, warning trong quá trình mình import dữ liệu để hiển thị lại sau này. Dựa vào đó ngưởi sử dụng biết lỗi do đâu để khắc phục ngay sau đó.
Và tất nhiên, ngay khi họ khắc phục xong cách vấn đề thì dữ liệu logs này không cần phải tồn tại nữa, chúng nên được giải phóng, biến mất đi.

Và đây đúng là vấn đề mà mình đã phải suy nghĩ và cân nhắc rất nhiều.

Bài viết này sẽ giới thiệu cho các bạn một số cách mà mình đã sử dụng.

# Lưu trực tiếp vào database

Đây là cách thông thường nhất mà mình đã nghĩ tới, lưu đơn giản và sau này cần lấy ra cũng thật là đơn giản, chỉ cần sử dụng các câu truy vấn, rồi muốn làm gì thì làm.
Nhưng như mình đã nói từ đầu, việc lưu trữ của mình chỉ cần trong 1 khoảng thời gian nhất định mà thôi, không phải lưu vĩnh viến, nếu lưu hết vào database thì thật sự rất rác. Vả lại mỗi lần lưu ID của chúng tự động tăng lên, dữ liệu lưu các nhiều, truy vấn sau này càng chậm.
Tất nhiên mình có thể viết 1 task riêng hoặc sử dụng CronJob để chạy lịch xoá chúng tự động sau một khoảng thời gian. Nhưng cái việc ID ngày càng tăng lên luôn khiến mình cảm thấy khó chịu và không dùng tới cách này.

# Lưu phía browser (LocalStorage, vv.vv)

Đây cũng là 1 vấn đề khi mình muốn lưu lại lịch sử làm bài trắc nghiệm của người dùng, mình muốn lưu lại lịch sử mỗi khi họ chọn đáp án để khi lỡ may F5 hay mất mạng thì không phải chọn lại từ đầu.

Thay vì chọn cách mỗi lần họ chọn đáp án sẽ submit lên server để lưu lại từng câu thì mình sử dụng localStorage của trình duyệt để lưu lại. Trường hợp họ submit cả bài làm thì mình mới gửi toàn bộ dữ liệu localStorage này lên để lưu lại dữ liệu.
Tuy nhiên, lại có vấn đề xảy ra ở đây là mình không thể quản lý được localStorage của người dùng, trường hợp có sự thay đổi trong cách lưu thì đó lại là cả một vấn đề phải xử lý vô cùng vất vả với những data đã tồn tại ở máy của người dùng. Đó là chưa kể dữ liệu localStorage nếu xử lý không cẩn thận rất dễ xảy ra vấn đề mà mình khó có thể Control được

Nhưng sau tất cả, mình đã chọn ra được một giải phát tốt nhất. Đó là.....

# Sử dụng Redis

"Redis là một hệ thống hỗ trợ lưu trữ dữ liệu và được lưu trữ trên ram để truy suất một cách nhanh chóng, nó hỗ trợ việc truy suất dữ liệu và một cách nhanh chóng. Và hiện nay, redis đã được dùng khá phổ biến cho lưu dữ liệu, Caching, Publish/Suscribe (Pub/Sub), Queues chạy Job "
