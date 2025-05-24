````markdown
## ☁️ AWS S3 + CloudFront Configuration for Django

Ứng dụng này sử dụng Amazon S3 để lưu trữ media/static files và CloudFront để phân phối nhanh hơn qua CDN.

---

### 1️⃣ Tạo Bucket S3

- Truy cập: https://s3.console.aws.amazon.com/s3/
- Nhấn **Create bucket**
- Đặt tên bucket (ví dụ: `my-bucket-name`)
- Region: chọn vùng phù hợp (ví dụ: `us-east-1`)
- **Tắt "Block all public access"** nếu dùng public file (hoặc cấu hình bằng policy sau)
- Nhấn **Create bucket**

---

### 2️⃣ Cấu hình Bucket Policy

Dùng đoạn sau nếu bạn muốn public truy cập file:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
````

Đừng quên thay `your-bucket-name` bằng tên thật.

---

### 3️⃣ Cài AWS CLI (nếu chưa có)

```bash
# macOS / Linux
brew install awscli
# hoặc Ubuntu
sudo apt install awscli
```

---

### 4️⃣ Đăng nhập AWS CLI

```bash
aws configure
```

Nhập:

* `AWS Access Key ID`
* `AWS Secret Access Key`
* `Default region`: `us-east-1` hoặc theo region bạn dùng
* `Default output`: để trống hoặc chọn `json`

---

### 5️⃣ Cài thư viện Python

```bash
pip install boto3 django-storages
```

---

### 6️⃣ Thêm vào Django

#### a. Thêm `'storages'` vào `INSTALLED_APPS` trong `settings.py`

```python
INSTALLED_APPS = [
    ...
    'storages',
]
```

#### b. Tạo file `.env`:

```env
# AWS credentials
AWS_ACCESS_KEY_ID=your-access-key
AWS_SECRET_ACCESS_KEY=your-secret-key
AWS_STORAGE_BUCKET_NAME=your-bucket-name
AWS_DEFAULT_REGION=us-east-1

# S3 Domain (nếu dùng CloudFront, thay bằng domain CloudFront)
AWS_S3_CUSTOM_DOMAIN=your-bucket-name.s3.amazonaws.com
```

---

### 7️⃣ Cấu hình `settings.py`

```python
import os

AWS_ACCESS_KEY_ID = os.getenv("AWS_ACCESS_KEY_ID")
AWS_SECRET_ACCESS_KEY = os.getenv("AWS_SECRET_ACCESS_KEY")
AWS_STORAGE_BUCKET_NAME = os.getenv("AWS_STORAGE_BUCKET_NAME")
AWS_DEFAULT_REGION = os.getenv("AWS_DEFAULT_REGION")
AWS_S3_CUSTOM_DOMAIN = os.getenv("AWS_S3_CUSTOM_DOMAIN")

STORAGES = {
    'default': {
        'BACKEND': 'storages.backends.s3boto3.S3Boto3Storage',
        'OPTIONS': {
            'access_key': AWS_ACCESS_KEY_ID,
            'secret_key': AWS_SECRET_ACCESS_KEY,
            'bucket_name': AWS_STORAGE_BUCKET_NAME,
            'region_name': AWS_DEFAULT_REGION,
        },
    },
    'staticfiles': {
        'BACKEND': 'storages.backends.s3boto3.S3StaticStorage',
        'OPTIONS': {
            'access_key': AWS_ACCESS_KEY_ID,
            'secret_key': AWS_SECRET_ACCESS_KEY,
            'bucket_name': AWS_STORAGE_BUCKET_NAME,
            'region_name': AWS_DEFAULT_REGION,
        },
    },
}

STATIC_URL = f"https://{AWS_S3_CUSTOM_DOMAIN}/static/"
MEDIA_URL = f"https://{AWS_S3_CUSTOM_DOMAIN}/media/"
```

---

### 8️⃣ (Tuỳ chọn) Cấu hình CloudFront

* Tạo CloudFront distribution trỏ đến S3 bucket
* Copy domain name của CloudFront (ví dụ: `d123abcxyz.cloudfront.net`)
* Cập nhật lại trong `.env`:

```env
AWS_S3_CUSTOM_DOMAIN=d123abcxyz.cloudfront.net
```

---

### ✅ Hoàn tất

Giờ đây, Django sẽ tự động dùng AWS S3 và CloudFront để lưu trữ và phân phối các tệp media và static.

> 🔐 Nếu bạn muốn dùng CloudFront signed URL (bảo mật link), hãy báo để mình hướng dẫn chi tiết thêm.

```
