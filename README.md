# veterinary-clinic-environment

Hạ tầng dev (PostgreSQL + Redis + MinIO) **dùng riêng cho project
veterinary-clinic-system**. Không dùng chung với bất kỳ project nào khác trên máy.

## Endpoints

| Dịch vụ | Cổng host | Ghi chú |
|---|---|---|
| PostgreSQL | `5433` | 5432 bị PostgreSQL 18 cài trực tiếp trên Windows chiếm |
| Redis | `6380` | 6379 bị một container của project khác chiếm |
| MinIO API | `9000` | |
| MinIO Console | `9001` | |

User/password mặc định: `vetclinic` / `vetclinic` (Postgres), `vetclinic` /
`vetclinic123` (MinIO) - xem `veterinary-clinic-backend/.env` để đổi.

## Chạy

```bat
docker compose up -d
```

Kiểm tra cả 3 đều `healthy`:

```bat
docker ps --filter "name=veterinary-clinic"
```

## Tắt

```bat
docker compose down
```

Giữ nguyên dữ liệu (named volume). Thêm `-v` nếu muốn xoá luôn dữ liệu - không phục
hồi được.

## Vì sao tách riêng thư mục này

`veterinary-clinic-backend`, `veterinary-clinic-web`, `veterinary-clinic-ai`,
`worker` mỗi cái là một tiến trình/repo độc lập. Hạ tầng dùng chung giữa
`veterinary-clinic-backend` và `worker` (Postgres, Redis) không thuộc về riêng ứng
dụng nào trong số đó, nên đặt ở thư mục riêng thay vì gắn vào một trong bốn thư mục
ứng dụng - tránh hiểu lầm "hạ tầng là của backend, xoá backend thì xoá luôn hạ tầng".

Backend/worker/ai kết nối vào các container ở đây bằng `localhost` + cổng đã remap
khi chạy trên host (`npm run start:dev`), hoặc qua `host.docker.internal` khi tự chạy
containerized (xem `veterinary-clinic-backend/docker-compose.yml`, dịch vụ có
`profiles: ['full']`).
