- Size của image chỉ ăn theo stage cuối cùng
- Nhiều stage thì dùng thẻ --target
- Bullseye phù hợp cho dev/debug (Bản Debian đầy đủ)
- Bullseye-slim phù hợp cho build (Bản Debian cắt bỏ bớt 40~50%)
- Distroless/alpine/scratch phù hợp cho production, nhẹ hơn, distroless và scratch thì bảo mật hơn
- Golang thì đéo có bullseye-slim nên đéo tối ưu hơn được nữa, thích thì dùng alpine để build
- 2 bên cung cấp distroless uy tín là gcr.io/distroless và cgr.dev/chainguard. Bên chainguard thì nhẹ hơn
- CMD chạy mỗi lần container start (docker run) còn RUN là chạy khi build
- CMD có thể bị ghi đè khi chạy docker run còn ENTRYPOINT thì phải dùng lệnh --entrypoint
- Bảo mật docker thì có 2 cách chính là bảo mật khi build image và bảo mật trong runtime. Build image thì cứ sài image nhỏ nhất
  có thể và build tới mức nhỏ nhất có thể. Runtime thì dùng dockerd (--userns-map) để root trong container đéo phải root trong
  host, lỡ bị chiếm container thì đ bị mất host, cái này config khi cài docker. 1 số option khác để bảo vệ container trong qtrinh
  run nữa.
- 4 thành phần bao gồm container, image, network, volume