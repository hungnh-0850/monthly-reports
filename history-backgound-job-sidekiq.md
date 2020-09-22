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
  scope :financial_jobs, -> { where(job_type: %w[financial_data_import]) }
end
```

## 3 - Tạo module phục vụ lưu Log

Module này sẽ chịu trách nhiệm cho hầu hết các công việc tạo và cập nhật lịch sử Job
```
module WithJobLogging
  def log_retryable_job(jid, job_type, user_id = nil)
    create_async_job_log(jid, job_type, user_id) unless async_job_log(jid)
    yield
    mark_job_as_finished(jid)
  end
  
  def log_nonretryable_job(jid, job_type, user_id = nil)
    begin
      create_async_job_log(jid, job_type, user_id) unless async_job_log(jid)
      yield
    rescue
      # if needed
    ensure
      mark_job_as_finished(jid)
    end
  end
  
  def create_async_job_log(jid, job_type, user_id = nil)
    AsyncJobLog.create(
      jid: jid,
      state: 'started',
      job_type: job_type,
      user_id: user_id
    )
  end

  def async_job_log(jid)
    AsyncJobLog.find_by(jid: jid)
  end

  def mark_job_as_finished(jid)
    async_job_log(jid).finished!
  end
end
```
Sử dụng mô-đun này trong worker thực tế:

```
require 'sidekiq'

class UserVerificationWorker
  include Sidekiq::Worker
  include WithJobLogging
  sidekiq_options retry: false

  def perform(user_id)
    log_nonretryable_job(jid, 'user_verification', user_id) { do_stuff }
  end
  
  private

  def do_stuff
    # logic
  end
end
```

## 4 - Mở rộng

```
require 'sidekiq'

class FinancialDataImportWorker
  include Sidekiq::Worker
  include WithJobLogging
  sidekiq_options retry: 5
  
  sidekiq_retries_exhausted do |msg|
    async_job_log = AsyncJobLog.find_by(jid: msg['jid'])
    async_job_log.finished!
  end

  def perform(user_id)
    user = User.find(user_id)
    return if financial_job_in_progress?(user)

    log_retryable_job(jid, 'financial_data_import', user_id) { do_stuff }
  end
  
  private

  def financial_job_in_progress?(user)
    user.async_job_logs.financial_jobs.started.where.not(jid: jid).any?
  end  
  
  def do_stuff
    # logic
  end
```
