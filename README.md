AWS
1. tạo bucket
2. tạo bucket policy (copy public access key và secret key)
3. tải aws cli
4. aws configure
5. tải thư viện `pip install boto3` và `pip install django-storages`
6. trong settings.py thêm `storages` trong install app
7. Thêm đoạn này trong file .env
```env
    # AWS API
    AWS_ACCESS_KEY_ID=
    AWS_SECRET_ACCESS_KEY=
    AWS_STORAGE_BUCKET_NAME=
    AWS_DEFAULT_REGION=
    AWS_S3_CUSTOM_DOMAIN='%s.s3.amazonaws.com' % AWS_STORAGE_BUCKET_NAME

8. Trong settings.py
```python
    STORAGES = {
        # Media file (image) management 
        'default': {
            'BACKEND': 'storages.backends.s3boto3.S3Boto3Storage',
            'OPTIONS': {
                'access_key': AWS_ACCESS_KEY_ID,
                'secret_key': AWS_SECRET_ACCESS_KEY,
                'bucket_name': AWS_STORAGE_BUCKET_NAME,
                'region_name': AWS_DEFAULT_REGION,
            },
        },
        # CSS and JS file management
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

CloudFront
```env
AWS_S3_CUSTOM_DOMAIN=
