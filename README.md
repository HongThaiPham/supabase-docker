# Supabase Docker build on Ubuntu 20.04

## Cập nhật hệ thống

```bash
sudo apt update
```

## Cài đặt Docker

[Docker guide](https://docs.docker.com/desktop/install/linux-install/)

```bash
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg

sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl enable docker.service
sudo systemctl enable containerd.service

sudo usermod -aG docker [user]

```

## Cập nhật các biến môi trường

```bash
mv .env.example .env
```

Mở file `.env`

```bash
vi .env
```

```bash
POSTGRES_PASSWORD=your-super-secret-and-long-postgres-password

JWT_SECRET=your-super-secret-jwt-token-with-at-least-32-characters-long
```

### Sử dụng công cụ tạo mã [Password generator](https://delinea.com/resources/password-generator-it-tool)

```bash
ANON_KEY=your-anon-key

SERVICE_ROLE_KEY=your-service-role-key
```

Dùng [Supabse API key generator](https://supabase.com/docs/guides/self-hosting#api-keys) để tạo ra các `ANON_KEY` và `SERVICE_ROLE_KEY`

### Cập nhật cấu hình Kong api

Mở file `volumes/api/kong.yml`, và cập nhật giá trị tương ứng các biến `ANON_KEY` và `SERVICE_ROLE_KEY`

```bash
consumers:
  - username: anon
    keyauth_credentials:
      - key: {ANON_KEY}
  - username: service_role
    keyauth_credentials:
      - key: {SERVICE_ROLE_KEY}
```

Biến `SITE_URL` là địa chỉ của trang web sử dụng Supabase

Các biến `SMTP_*` là các thông tin cấu hình gửi mail

Biến `API_EXTERNAL_URL` là địa chỉ của api Kong

Biến `ENABLE_EMAIL_AUTOCONFIRM` đặt là `true` để tự động xác nhận email

`STUDIO_DEFAULT_ORGANIZATION` là tên của tổ chức mặc định

`STUDIO_DEFAULT_PROJECT` là tên của dự án mặc định

`SUPABASE_PUBLIC_URL` là địa chỉ của trang studio

## Cấu hình basic auth cho trang studio

Mở file `nginx/.htpasswd` và cập nhật giá trị bên trong sử dụng công cụ [Htpasswd Generator](https://hostingcanada.org/htpasswd-generator/)

```bash
supabase:$apr1$9ejxqda1$gF59jvL2m75ZLM7Att/S./
```

Giá trị trên là
username: supabase
password:supabase

## Cập nhật domain

Mở file `nginx/templates/studio.conf.template` và cập nhật giá trị cho biến `server_name`

## Chạy docker-compose

```bash
docker-compose up -d
```

## Tools

[Supabse API key generator](https://supabase.com/docs/guides/self-hosting#api-keys)

[Password generator](https://delinea.com/resources/password-generator-it-tool)

[Htpasswd Generator](https://hostingcanada.org/htpasswd-generator/)
