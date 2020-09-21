Trong khi phát triển các ứng dụng Web. Có lẽ chúng ta đã quen với các tác vụ cần xử lý Background, điển hình khi sử dụng Rails, có lẽ mỗ chúng ta đã từng dùng qua
Sidekiq

Sidekiq đã giúp chúng ta lưu lại lịch sử các Job teo từng trạng thái và khá cụ thể. Tuy nhiên nhưng những nội dung này xấu, khó đọc hoặc
không lưu trữ đúng các dự liệu bạn cần.

Bài viết này sẽ cung cấp cho các bạn ví dụ về cách lưu lại các Job Sidekiq để bạn có thể sử dụng cho những mục đích khác nhau.

# 1 - Tạo bảng lưu lịch sử
Chúng ta sẽ xử lý để lưu lịch sử vào cơ sở dữ liệu. Ở đây mình sẽ sử dụng bảng có tên là `async_job_logs`
Cấu trúc có thể phụ thuộc vào nhu cầu của bạn. Tuy nhiên ở đây mình sẽ có các trường cơ bản bao gồm: `job_type` (loại công việc), 
`state` (trạng thái hiện tại của công việc), `jid` (ID công việc), `user_id` và `create_at / updated_at`.

Và hãy đánh index cho cột `jid`và chúng ta sẽ cần sử dụng làm điều kiện để query sau này.

```class CreateAsyncJobLogs < ActiveRecord::Migration[5.0]
  def change
    create_table :async_job_logs do |t|
      t.string  :jid
      t.integer :state
      t.integer :job_type
      t.references :user, foreign_key: true

      t.timestamps
    end
  end
  
  add_index(:async_job_logs, :jid)
end
```


# 2 - Tạo model
Ở đây mình sẽ tạo thêm model `AsyncJobLog` với enums state. Bạn có thể muốn thêm nhiều trạng thái khác như `failed` hoặc `dead`. 
Ở đây để đơn giản, mình sẽ chỉ sử dụng `started` và `finish`. 

```
class AsyncJobLog < ApplicationRecord
  enum state: {
    started:  1,
    finished: 2,
  }

  enum job_type: {
    user_verification:       1,
    financial_data_import:   2,
  }
end
```
