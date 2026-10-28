# BÁO CÁO CHI TIẾT: MÔ PHỎNG THANG MÁY BẰNG THUẬT TOÁN SCAN/LOOK

## 1. Giới thiệu bài toán

Bài toán yêu cầu mô phỏng hoạt động của một thang máy trong tòa nhà có `n` tầng. Thang máy có một tầng xuất phát, một tải trọng tối đa và có thể nhận hai nhóm yêu cầu:

- **Yêu cầu gọi thang từ ngoài sảnh:** Người dùng đang ở một tầng và gọi thang theo hướng đi lên hoặc đi xuống.
- **Yêu cầu chọn tầng từ bên trong cabin:** Hành khách đã ở trong thang máy và chọn tầng muốn đến.

Chương trình cần mô phỏng quá trình thang máy di chuyển, đón khách, trả khách, đổi chiều và phát hiện tình trạng quá tải. Sau mỗi hành động, chương trình ghi lại trạng thái theo định dạng:

` tầng_hiện_tại hướng_hiện_tại mã_hành_động `

Báo cáo này trình bày cách hiểu dữ liệu, ý tưởng thuật toán, cấu trúc dữ liệu, các hàm xử lý, luồng chạy của chương trình, độ phức tạp, ví dụ minh họa và những điểm cần lưu ý khi đánh giá lời giải.

## 2. Phân tích đề bài

### 2.1. Dữ liệu đầu vào

Dòng đầu tiên chứa ba số nguyên:

- `n`: số tầng của tòa nhà.
- `max_taitrong`: tải trọng tối đa cho phép của thang máy.
- `tang_ht`: tầng bắt đầu của thang máy.

Các dòng tiếp theo là những yêu cầu có dạng `(loai_yc, ds1, ds2)`.

| `loai_yc` | Ý nghĩa | `ds1` | `ds2` |
|---|---|---|---|
| `1` | Gọi thang từ ngoài sảnh | Tầng gọi thang | Hướng gọi: `1` là lên, `-1` là xuống |
| `2` | Chọn tầng từ cabin | Tầng muốn đến | Trọng lượng khách |
| `0` | Kết thúc nhập dữ liệu | Theo đề, bằng `0` | Theo đề, bằng `0` |

Khi đọc được bộ ba `0 0 0`, chương trình dừng nhập dữ liệu và bắt đầu mô phỏng, trừ trường hợp đã phát hiện quá tải trong lúc đọc.

### 2.2. Ý nghĩa hướng di chuyển

Chương trình dùng biến `huong_ht` để lưu hướng hiện tại:

- `huong_ht = 1`: thang máy đang đi lên.
- `huong_ht = -1`: thang máy đang đi xuống.

Mỗi lần thang máy di chuyển sang tầng tiếp theo, tầng hiện tại được cập nhật bằng cách cộng hướng di chuyển:

- Đi lên: `tang_ht = tang_ht + 1`.
- Đi xuống: `tang_ht = tang_ht - 1`.

Trong chương trình, hướng ban đầu được khởi tạo là `1`, nghĩa là thang máy bắt đầu với hướng đi lên.

### 2.3. Ý nghĩa mã hành động đầu ra

| Mã | Ý nghĩa |
|---|---|
| `0` | Thang máy đi qua tầng hiện tại, không dừng để phục vụ |
| `1` | Thang máy dừng tại tầng hiện tại để đón hoặc trả khách |
| `2` | Thang máy đổi hướng di chuyển |
| `3` | Hệ thống phát hiện quá tải; in trạng thái và kết thúc chương trình |

Mỗi dòng đầu ra có ba giá trị: tầng hiện tại, hướng hiện tại và mã hành động. Khi mã hành động là `2`, chương trình cập nhật hướng rồi in hướng mới cùng tầng đang đứng.

## 3. Mô hình hóa bài toán

### 3.1. Hai nhóm yêu cầu

Có thể chia các yêu cầu thành hai nhóm riêng biệt.

**Nhóm 1 — yêu cầu chọn tầng trong cabin**

Khi hành khách chọn tầng đến, chương trình đánh dấu tầng đó là có yêu cầu. Khi thang máy thực sự đến tầng này, chương trình xóa dấu đánh dấu và ghi nhận hành động dừng.

Trong lời giải, nhóm này được lưu bằng:

`vector<bool> yc_trong`

Phần tử `yc_trong[t]` bằng `true` có nghĩa là có yêu cầu trong cabin cần được phục vụ tại tầng `t`.

**Nhóm 2 — yêu cầu gọi thang ngoài sảnh**

Mỗi yêu cầu gồm tầng gọi và hướng muốn đi. Ví dụ, yêu cầu `(tầng 4, hướng 1)` là gọi thang tại tầng 4 để đi lên.

Nhóm này được lưu bằng danh sách:

`vector<yc_ngoai> yc`

Trong đó, mỗi phần tử là một cấu trúc chứa `tang` và `huong`.

### 3.2. Trạng thái toàn hệ thống

Tại một thời điểm, trạng thái mô phỏng được xác định bởi:

- `tang_ht`: tầng hiện tại.
- `huong_ht`: hướng hiện tại.
- `yc_trong`: các tầng đến được chọn từ cabin nhưng chưa phục vụ.
- `yc`: các yêu cầu gọi thang ngoài sảnh còn tồn tại.

Chương trình tiếp tục chạy khi ít nhất một trong hai nhóm yêu cầu vẫn còn. Khi không còn yêu cầu nào, quá trình mô phỏng kết thúc.

## 4. Ý tưởng thuật toán SCAN/LOOK

### 4.1. Nguyên tắc chung

SCAN và LOOK là các thuật toán phục vụ yêu cầu theo hướng di chuyển. Với cách mô phỏng trong chương trình, thang máy:

1. Xem xét các yêu cầu tại tầng hiện tại.
2. Nếu có yêu cầu phù hợp, dừng và phục vụ; nếu không, đi qua tầng.
3. Kiểm tra xem phía trước theo hướng đang đi còn yêu cầu hay không.
4. Nếu phía trước không còn yêu cầu nhưng toàn hệ thống vẫn còn yêu cầu, đổi chiều.
5. Nếu vẫn còn yêu cầu, di chuyển một tầng theo hướng hiện tại.
6. Lặp lại cho đến khi toàn bộ yêu cầu được xử lý.

Điểm quan trọng là thang máy không quay đầu chỉ vì vừa phục vụ xong một yêu cầu. Nó tiếp tục theo hướng hiện tại nếu vẫn còn yêu cầu phía trước theo cách kiểm tra của chương trình.

### 4.2. Phân biệt SCAN và LOOK

- **SCAN:** thường tiếp tục đến tận cuối hành trình hoặc biên của phạm vi, rồi mới đổi hướng.
- **LOOK:** chỉ đi đến vị trí yêu cầu xa nhất theo hướng hiện tại rồi đổi hướng, không nhất thiết phải đi đến tầng biên nếu không còn yêu cầu ở xa hơn.

Chương trình trong báo cáo có hàm `co_yc_theo_chieu_ht()` kiểm tra các yêu cầu nằm phía trước theo hướng hiện tại. Vì vậy, logic kiểm tra này gần với cách tiếp cận **LOOK** hơn là SCAN thuần túy: thang máy có thể đổi hướng khi không còn yêu cầu ở phía trước, thay vì bắt buộc đi đến tầng cao nhất hoặc thấp nhất.

### 4.3. Quy tắc phục vụ yêu cầu ngoài sảnh

Chương trình chỉ phục vụ yêu cầu ngoài sảnh khi cả hai điều kiện đều đúng:

- Tầng gọi bằng tầng hiện tại.
- Hướng gọi bằng hướng hiện tại.

Ví dụ, nếu thang máy đang đi lên và gặp một yêu cầu gọi thang tại tầng hiện tại với hướng đi xuống, yêu cầu đó chưa được xóa trong lần xử lý này. Yêu cầu chỉ được phục vụ khi thang máy đang ở tầng đó với hướng phù hợp.

### 4.4. Thời điểm xóa yêu cầu

Yêu cầu chọn tầng trong cabin chỉ được xóa khi thang máy đến đúng tầng đích và xử lý tầng đó. Tương tự, yêu cầu ngoài sảnh chỉ được xóa khi thang máy đến đúng tầng gọi và hướng hiện tại khớp với hướng gọi.

Đây là nguyên tắc quan trọng để tránh xóa yêu cầu trước khi thang máy thực sự phục vụ.

## 5. Phân tích cấu trúc dữ liệu

### 5.1. Cấu trúc `yc_ngoai`

```cpp
struct yc_ngoai {
    int tang;
    int huong;
};
```

Cấu trúc này biểu diễn một yêu cầu gọi thang ngoài sảnh:

- `tang`: tầng có người đang chờ.
- `huong`: hướng mà người đó muốn đi.

Việc gộp hai thông tin vào một cấu trúc giúp mỗi yêu cầu được lưu và xử lý như một đơn vị thống nhất.

### 5.2. Mảng đánh dấu `yc_trong`

```cpp
vector<bool> yc_trong;
```

Mảng được cấp phát với `n + 1` phần tử để có thể sử dụng trực tiếp chỉ số tầng từ `1` đến `n`. Phần tử tại chỉ số `0` không đại diện cho tầng hợp lệ trong mô hình này.

Khi khởi tạo:

```cpp
yc_trong.assign(n + 1, false);
```

tất cả các tầng ban đầu đều chưa có yêu cầu chọn tầng từ cabin.

Khi đọc yêu cầu chọn tầng đến `tang_den`:

```cpp
yc_trong[tang_den] = true;
```

Khi thang máy đến tầng có yêu cầu:

```cpp
yc_trong[tang_ht] = false;
```

Lưu ý: cách đánh dấu bằng `bool` chỉ lưu được thông tin có hoặc không có yêu cầu ở một tầng. Nếu có nhiều hành khách độc lập cùng chọn một tầng, nhiều yêu cầu cùng tầng sẽ bị gộp thành một dấu `true`.

### 5.3. Danh sách `yc`

```cpp
vector<yc_ngoai> yc;
```

Danh sách này lưu các yêu cầu ngoài sảnh chưa được phục vụ. Nó cho phép chương trình duyệt qua từng yêu cầu để tìm những yêu cầu khớp với tầng và hướng hiện tại.

Khi xóa một phần tử trong `vector` bằng `erase`, iterator đang trỏ đến phần tử bị xóa không còn hợp lệ. Vì vậy, chương trình phải nhận iterator trả về từ `erase`:

```cpp
it = yc.erase(it);
```

Nếu không xóa phần tử, chương trình mới tăng iterator:

```cpp
++it;
```

Cách viết này giúp tránh bỏ qua phần tử kế tiếp hoặc sử dụng iterator không hợp lệ.

## 6. Phân tích các hàm xử lý

### 6.1. Hàm `kt_con_yc_nao_khong()`

**Mục đích:** Kiểm tra xem trong hệ thống còn bất kỳ yêu cầu nào chưa được phục vụ hay không.

Hàm thực hiện hai bước:

1. Nếu danh sách yêu cầu ngoài sảnh `yc` không rỗng, trả về `true`.
2. Nếu danh sách ngoài sảnh rỗng, duyệt các tầng từ `1` đến `n`. Nếu tìm thấy `yc_trong[i] == true`, trả về `true`.
3. Nếu không tìm thấy yêu cầu nào, trả về `false`.

Hàm này được sử dụng làm điều kiện cho vòng lặp mô phỏng chính. Khi trả về `false`, thang máy không còn công việc cần xử lý và vòng lặp kết thúc.

### 6.2. Hàm `co_yc_theo_chieu_ht()`

**Mục đích:** Kiểm tra có yêu cầu nào nằm phía trước theo hướng di chuyển hiện tại hay không.

Nếu đang đi lên (`huong_ht == 1`):

- Duyệt các tầng lớn hơn `tang_ht` để tìm yêu cầu trong cabin.
- Duyệt danh sách yêu cầu ngoài sảnh để tìm yêu cầu có tầng lớn hơn `tang_ht`.

Nếu đang đi xuống (`huong_ht == -1`):

- Duyệt các tầng nhỏ hơn `tang_ht` để tìm yêu cầu trong cabin.
- Duyệt danh sách yêu cầu ngoài sảnh để tìm yêu cầu có tầng nhỏ hơn `tang_ht`.

Nếu có ít nhất một yêu cầu phía trước, hàm trả về `true`; nếu không, trả về `false`.

**Vai trò trong thuật toán:** Hàm này giúp quyết định thang máy nên tiếp tục theo hướng cũ hay đổi chiều. Nó chỉ kiểm tra vị trí tầng của yêu cầu, không kiểm tra hướng gọi ngoài sảnh trong bước này. Do đó, một yêu cầu ngoài sảnh nằm phía trước nhưng gọi theo hướng ngược lại vẫn được xem là có yêu cầu phía trước; thang máy có thể đi đến tầng đó trước, nhưng chỉ phục vụ yêu cầu khi hướng hiện tại khớp với hướng gọi.

### 6.3. Hàm `xu_ly_tai_tang()`

**Mục đích:** Xử lý tất cả yêu cầu có thể được phục vụ tại tầng hiện tại.

Ban đầu, hàm đặt:

```cpp
bool dung = false;
```

Biến `dung` cho biết thang máy có phải dừng ở tầng hiện tại hay không.

**Bước 1 — Xử lý yêu cầu trong cabin**

Nếu `yc_trong[tang_ht]` bằng `true`, chương trình:

- Đặt `dung = true`.
- Xóa yêu cầu tại tầng hiện tại bằng cách gán `yc_trong[tang_ht] = false`.

Việc xóa chỉ diễn ra khi thang máy đã ở đúng tầng đích.

**Bước 2 — Xử lý yêu cầu ngoài sảnh**

Chương trình duyệt danh sách `yc`. Nếu một yêu cầu có tầng và hướng đều khớp với trạng thái hiện tại, chương trình:

- Đặt `dung = true`.
- Xóa yêu cầu khỏi danh sách bằng `erase`.

Các yêu cầu không khớp được giữ lại để xử lý ở thời điểm khác.

**Bước 3 — Ghi nhật ký**

- Nếu `dung == true`, in mã hành động `1`.
- Nếu `dung == false`, in mã hành động `0`.

Do đó, một tầng có thể đồng thời phục vụ yêu cầu trong cabin và yêu cầu ngoài sảnh; chương trình chỉ cần ghi một dòng hành động dừng cho tầng đó.

### 6.4. Kiểm tra quá tải khi nhập dữ liệu

Khi đọc yêu cầu loại `2`, chương trình lấy:

- `ds1` làm tầng đến.
- `ds2` làm trọng lượng khách.

Nếu `tai_trong > max_taitrong`, chương trình in:

```text
tang_ht huong_ht 3
```

sau đó kết thúc ngay bằng `return 0`.

Trong chương trình hiện tại, đây là phép so sánh trọng lượng của từng khách với tải trọng tối đa. Nó chưa tính tổng trọng lượng của nhiều hành khách đang ở trong cabin. Đây là một giới hạn quan trọng nếu muốn mô phỏng tải trọng thực tế của thang máy.

## 7. Luồng thực thi của chương trình chính

### 7.1. Khởi tạo

Chương trình đọc `n`, `max_taitrong` và `tang_ht`. Nếu không đọc được dữ liệu đầu vào hợp lệ ở bước đầu, chương trình kết thúc.

Sau đó, mảng `yc_trong` được khởi tạo với `n + 1` phần tử và tất cả đều bằng `false`. Hướng ban đầu được đặt là đi lên.

### 7.2. Đọc và lưu yêu cầu

Chương trình đọc từng bộ ba `loai_yc`, `ds1`, `ds2`:

- Nếu là `1`, thêm yêu cầu gọi thang vào `yc`.
- Nếu là `2`, kiểm tra quá tải; nếu không quá tải thì đánh dấu tầng đến trong `yc_trong`.
- Nếu là `0 0 0`, kết thúc giai đoạn nhập.

Vì chương trình đọc và lưu các yêu cầu trước khi mô phỏng, các yêu cầu được xem như đã có sẵn trong hệ thống tại thời điểm bắt đầu mô phỏng. Chương trình không mô phỏng thời điểm phát sinh yêu cầu theo thời gian thực.

### 7.3. Vòng lặp mô phỏng

Vòng lặp chính chạy khi `kt_con_yc_nao_khong()` trả về `true`. Mỗi vòng gồm ba phần:

**Phần 1 — Phục vụ tầng hiện tại**

Gọi `xu_ly_tai_tang()` để kiểm tra và xử lý yêu cầu tại tầng đang đứng, sau đó in mã `0` hoặc `1`.

**Phần 2 — Kiểm tra đổi chiều**

Nếu không còn yêu cầu phía trước theo hướng hiện tại nhưng toàn hệ thống vẫn còn yêu cầu, chương trình đảo hướng:

```cpp
huong_ht = -huong_ht;
```

Sau đó in mã hành động `2` tại tầng hiện tại với hướng mới.

**Phần 3 — Di chuyển**

Nếu hệ thống vẫn còn yêu cầu, cập nhật tầng hiện tại:

```cpp
tang_ht += huong_ht;
```

Khi tất cả yêu cầu đã được xử lý, điều kiện vòng lặp sai và chương trình kết thúc.

### 7.4. Sơ đồ luồng bằng lời

1. Đọc thông số tòa nhà và tầng xuất phát.
2. Đọc các yêu cầu.
3. Nếu phát hiện quá tải theo điều kiện của chương trình, in mã `3` và kết thúc.
4. Trong khi còn yêu cầu:
   - Xử lý yêu cầu tại tầng hiện tại.
   - Nếu phía trước không còn yêu cầu và hệ thống vẫn còn việc, đổi hướng.
   - Nếu vẫn còn việc, đi sang tầng kế tiếp.
5. Kết thúc khi không còn yêu cầu.

## 8. Minh họa cách hoạt động

Xét ví dụ minh họa sau:

```text
6 500 2
2 5 60
1 4 1
0 0 0
```

Ý nghĩa:

- Tòa nhà có 6 tầng.
- Tải trọng tối đa là 500.
- Thang máy bắt đầu tại tầng 2.
- Có yêu cầu trong cabin đi đến tầng 5, trọng lượng khách là 60.
- Có yêu cầu gọi thang ở tầng 4 theo hướng đi lên.
- `0 0 0` kết thúc nhập dữ liệu.

Với hướng ban đầu đi lên, cách xử lý theo logic chương trình là:

1. Tầng 2: chưa có yêu cầu cần phục vụ, nên ghi mã `0`.
2. Phía trước còn yêu cầu ở tầng 4 và tầng 5, vì vậy thang máy tiếp tục đi lên.
3. Tầng 3: chưa có yêu cầu cần phục vụ, nên ghi mã `0`.
4. Tầng 4: yêu cầu gọi thang hướng lên khớp với hướng hiện tại, nên dừng và ghi mã `1`. Yêu cầu tầng 4 được xóa.
5. Tầng 5: yêu cầu chọn tầng trong cabin được phục vụ, nên dừng và ghi mã `1`. Yêu cầu tầng 5 được xóa.
6. Không còn yêu cầu nào nên vòng lặp kết thúc.

Ví dụ này minh họa nguyên tắc đi theo hướng hiện tại, chỉ dừng khi có yêu cầu phù hợp và kết thúc khi mọi yêu cầu đã được xử lý. Nhật ký chính xác phụ thuộc vào thứ tự các bước kiểm tra và in trong mã chương trình.

## 9. Phân tích tính đúng đắn

Có thể xem xét tính đúng đắn của lời giải qua các điểm sau.

### 9.1. Không kết thúc khi vẫn còn yêu cầu

Hàm `kt_con_yc_nao_khong()` kiểm tra cả danh sách yêu cầu ngoài sảnh lẫn mảng đánh dấu yêu cầu trong cabin. Vì vậy, nếu một trong hai nhóm vẫn còn yêu cầu, vòng lặp tiếp tục chạy.

### 9.2. Chỉ xóa yêu cầu khi phục vụ

Yêu cầu trong cabin chỉ bị xóa tại tầng đích. Yêu cầu ngoài sảnh chỉ bị xóa khi tầng gọi và hướng gọi khớp với tầng và hướng hiện tại. Điều này giữ lại các yêu cầu chưa được phục vụ.

### 9.3. Chỉ đổi hướng khi không còn yêu cầu phía trước

Chương trình gọi `co_yc_theo_chieu_ht()`. Khi hàm trả về `false` nhưng hệ thống vẫn còn yêu cầu, chương trình đảo hướng. Điều này phù hợp với ý tưởng LOOK: xử lý các yêu cầu theo hướng hiện tại rồi mới quay lại xử lý các yêu cầu phía sau.

### 9.4. Kết thúc khi không còn yêu cầu

Khi cả `yc` rỗng và tất cả phần tử `yc_trong[1..n]` đều bằng `false`, hàm kiểm tra trả về `false`, khiến vòng lặp mô phỏng dừng.

## 10. Độ phức tạp thuật toán

Gọi:

- `n` là số tầng.
- `m` là số yêu cầu ngoài sảnh.
- `k` là số bước mô phỏng, tức số lần xử lý tầng trong vòng lặp.

### 10.1. Bộ nhớ

- Mảng `yc_trong` dùng bộ nhớ tỷ lệ với số tầng: O(n).
- Danh sách `yc` dùng bộ nhớ tỷ lệ với số yêu cầu ngoài sảnh: O(m).

Tổng bộ nhớ phụ trợ là O(n + m), không tính bộ nhớ dùng để lưu dữ liệu đầu vào bên ngoài các cấu trúc này.

### 10.2. Thời gian

- `kt_con_yc_nao_khong()` có thể duyệt đến `n` tầng, đồng thời kiểm tra danh sách `yc`.
- `co_yc_theo_chieu_ht()` có thể duyệt các tầng phía trước và toàn bộ danh sách yêu cầu ngoài sảnh.
- `xu_ly_tai_tang()` duyệt danh sách yêu cầu ngoài sảnh để tìm và xóa yêu cầu phù hợp.

Mỗi bước mô phỏng có thể cần thời gian O(n + m) theo cách cài đặt hiện tại. Với `k` bước mô phỏng, có thể ước lượng thời gian là O(k(n + m)), chưa tính chi tiết chi phí dịch chuyển phần tử của `vector` khi gọi `erase`. Việc xóa phần tử giữa `vector` có thể làm các phần tử phía sau dịch chuyển, nên tổng chi phí thực tế có thể cao hơn khi có nhiều yêu cầu bị xóa.

Đây là đánh giá theo cách cài đặt hiện tại; nếu cần tối ưu cho dữ liệu lớn, có thể dùng cấu trúc dữ liệu phù hợp hơn hoặc duy trì các nhóm yêu cầu theo tầng và hướng.

## 11. Các trường hợp cần kiểm thử

Nên kiểm thử tối thiểu các tình huống sau:

1. **Không có yêu cầu:** Sau thông số ban đầu là `0 0 0`. Chương trình không cần mô phỏng hành trình.
2. **Chỉ có yêu cầu trong cabin:** Thang máy phải đi đến tầng được chọn và xóa yêu cầu khi đến nơi.
3. **Chỉ có yêu cầu ngoài sảnh cùng hướng:** Thang máy đến tầng gọi, dừng và xóa yêu cầu.
4. **Yêu cầu ở cả phía trước và phía sau:** Thang máy xử lý phía trước theo hướng hiện tại, sau đó đổi hướng khi cần.
5. **Yêu cầu gọi ngoài sảnh ngược hướng:** Kiểm tra rằng yêu cầu không bị xóa khi hướng hiện tại không khớp.
6. **Nhiều yêu cầu ngoài sảnh tại cùng tầng:** Kiểm tra việc duyệt và xóa nhiều phần tử trong `vector`.
7. **Nhiều yêu cầu trong cabin cùng tầng đích:** Xác định rõ liệu đề bài cho phép gộp yêu cầu theo tầng hay phải lưu từng yêu cầu riêng.
8. **Trọng lượng khách lớn hơn giới hạn:** Chương trình in mã `3` và kết thúc.
9. **Trọng lượng khách bằng giới hạn:** Theo điều kiện hiện tại, không bị xem là quá tải vì chỉ kết thúc khi `tai_trong > max_taitrong`.
10. **Yêu cầu tại tầng hiện tại:** Kiểm tra việc phục vụ ngay tại tầng hiện tại và khả năng đổi hướng nếu cần.
11. **Yêu cầu nằm ở tầng biên:** Kiểm tra các tầng `1` và `n`, tránh di chuyển ra ngoài phạm vi tòa nhà.
12. **Nhiều yêu cầu gọi cùng tầng nhưng khác hướng:** Kiểm tra chúng được giữ lại và phục vụ theo hướng phù hợp.

## 12. Những hạn chế và điểm cần xác minh

Phần này phân biệt hành vi thực tế của mã nguồn với những gì một hệ thống thang máy hoàn chỉnh có thể cần.

### 12.1. Tải trọng được kiểm tra theo từng khách

Mã hiện tại so sánh trọng lượng của từng khách với `max_taitrong`. Nó không cộng dồn trọng lượng các hành khách đã ở trong cabin. Nếu đề bài yêu cầu kiểm tra tổng tải trọng, cần duy trì tổng trọng lượng hiện tại và cập nhật khi đón/trả khách. Tuy nhiên, dữ liệu đầu vào hiện tại chưa mô tả đầy đủ thời điểm lên/xuống và trạng thái hành khách để xây dựng mô hình tải trọng chính xác.

### 12.2. Yêu cầu trong cabin bị gộp theo tầng

`vector<bool>` chỉ biểu diễn có hoặc không có yêu cầu ở một tầng. Hai yêu cầu cùng tầng không thể được phân biệt. Nếu đề bài yêu cầu lưu từng lần chọn tầng, nên dùng bộ đếm hoặc danh sách yêu cầu thay vì cờ Boolean.

### 12.3. Yêu cầu được nhập hết trước khi mô phỏng

Chương trình không xử lý các yêu cầu phát sinh trong khi thang máy đang di chuyển. Tất cả yêu cầu được lưu trước, sau đó mới bắt đầu hành trình. Đây là mô phỏng một tập yêu cầu tĩnh, không phải hệ thống thời gian thực.

### 12.4. Hướng gọi được kiểm tra khi phục vụ, nhưng không dùng khi tìm yêu cầu phía trước

Trong `co_yc_theo_chieu_ht()`, yêu cầu ngoài sảnh chỉ được kiểm tra theo vị trí tầng, không xét `yc[i].huong`. Vì vậy, yêu cầu ngược hướng ở phía trước vẫn khiến thang máy tiếp tục đi đến phía đó. Đây là hành vi của mã hiện tại; cần đối chiếu với quy định chính xác của đề bài để xác định có đúng yêu cầu hay không.

### 12.5. Tải trọng quá giới hạn kết thúc ngay khi nhập

Nếu phát hiện một khách có trọng lượng lớn hơn tải trọng tối đa, chương trình in tầng và hướng hiện tại rồi kết thúc ngay, ngay cả khi các yêu cầu trước đó đã được đọc nhưng chưa mô phỏng. Cách xử lý này đúng với quy tắc “phát hiện quá tải thì dừng chương trình” được mô tả trong tài liệu đầu vào, nhưng cần xác nhận định dạng đầu ra với đề gốc.

### 12.6. Kiểm tra dữ liệu đầu vào

Mã hiện tại chưa kiểm tra đầy đủ các điều kiện như:

- `1 <= tang_ht <= n`.
- Tầng gọi và tầng đến nằm trong phạm vi `1..n`.
- Hướng gọi chỉ nhận `1` hoặc `-1`.
- Dữ liệu đầu vào không bị thiếu giữa chừng.

Nếu đề bài bảo đảm dữ liệu luôn hợp lệ, các kiểm tra này có thể không bắt buộc. Nếu không, cần bổ sung để tránh truy cập ngoài phạm vi mảng hoặc trạng thái không hợp lệ.

## 13. Hướng cải tiến

Tùy theo yêu cầu chính thức của đề bài, có thể cân nhắc:

1. **Lưu yêu cầu trong cabin theo số lượng:** Dùng bộ đếm cho từng tầng hoặc danh sách từng hành khách nếu cần phân biệt nhiều yêu cầu đến cùng tầng.
2. **Tối ưu việc tìm yêu cầu phía trước:** Thay vì mỗi lần quét toàn bộ danh sách, có thể tổ chức yêu cầu theo tầng và hướng.
3. **Tách kiểm tra quá tải khỏi bước đọc dữ liệu:** Nếu mô hình yêu cầu kiểm tra tải trọng khi hành khách thực sự lên thang, nên kiểm tra tại thời điểm đón khách thay vì chỉ kiểm tra lúc đọc yêu cầu.
4. **Xác định rõ quy tắc xử lý yêu cầu ngược hướng:** Cần thống nhất thang máy có đi đến tầng có người gọi ngược hướng hay chỉ phục vụ yêu cầu phù hợp với chiều đang chạy.
5. **Kiểm tra biên tầng và dữ liệu đầu vào:** Bảo đảm thang máy không di chuyển xuống tầng `0` hoặc lên tầng `n + 1`.
6. **Viết hàm mô phỏng rõ trách nhiệm:** Có thể tách các phần đọc dữ liệu, kiểm tra tải trọng, phục vụ tầng, chọn hướng và ghi nhật ký thành các hàm riêng để dễ kiểm thử và bảo trì.

Không nên áp dụng các cải tiến này một cách máy móc nếu đề gốc quy định hành vi khác; cần lấy mô tả đề bài làm tiêu chuẩn cuối cùng.

## 14. Kết luận

Bài toán mô phỏng thang máy có thể được giải quyết bằng cách mô hình hóa các yêu cầu trong cabin và ngoài sảnh thành hai cấu trúc dữ liệu riêng, sau đó mô phỏng chuyển động theo từng tầng. Thuật toán LOOK/SCAN cung cấp nguyên tắc điều hướng: tiếp tục phục vụ theo hướng hiện tại khi còn yêu cầu phía trước, đổi hướng khi cần và kết thúc khi toàn bộ yêu cầu đã được xử lý.

Các hàm `kt_con_yc_nao_khong()`, `co_yc_theo_chieu_ht()` và `xu_ly_tai_tang()` chia bài toán thành những phần nhỏ, giúp chương trình dễ hiểu và kiểm tra. Để bảo đảm lời giải khớp hoàn toàn với đề gốc, cần đặc biệt xác minh cách tính tải trọng, cách biểu diễn nhiều yêu cầu cùng tầng, quy tắc phục vụ yêu cầu gọi ngược hướng và định dạng nhật ký ở thời điểm đổi chiều.

---

## Phụ lục. Mã nguồn được phân tích

Mã nguồn dưới đây được giữ theo nội dung tài liệu đầu vào để có thể đối chiếu với phần giải thích.

```cpp
#include <iostream>
#include <vector>
#include <cmath>

using namespace std;

struct yc_ngoai {
    int tang;
    int huong;
};

int n, max_taitrong;
int tang_ht;
int huong_ht = 1; // 1: Đi lên, -1: Đi xuống

vector<bool> yc_trong;
vector<yc_ngoai> yc;

bool kt_con_yc_nao_khong() {
    if (!yc.empty()) return true;
    for (int i = 1; i <= n; i++) {
        if (yc_trong[i] == true) return true;
    }
    return false;
}

bool co_yc_theo_chieu_ht() {
    if (huong_ht == 1) {
        for (int i = tang_ht + 1; i <= n; i++) {
            if (yc_trong[i] == true) return true;
        }
        for (size_t i = 0; i < yc.size(); i++) {
            if (yc[i].tang > tang_ht) return true;
        }
    } else if (huong_ht == -1) {
        for (int i = tang_ht - 1; i >= 1; i--) {
            if (yc_trong[i] == true) return true;
        }
        for (size_t i = 0; i < yc.size(); i++) {
            if (yc[i].tang < tang_ht) return true;
        }
    }
    return false;
}

void xu_ly_tai_tang() {
    bool dung = false;

    if (yc_trong[tang_ht] == true) {
        dung = true;
        yc_trong[tang_ht] = false;
    }

    for (auto it = yc.begin(); it != yc.end(); ) {
        if (it->tang == tang_ht && it->huong == huong_ht) {
            dung = true;
            it = yc.erase(it);
        } else {
            ++it;
        }
    }

    if (dung) cout << tang_ht << " " << huong_ht << " 1\n";
    else cout << tang_ht << " " << huong_ht << " 0\n";
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    if (!(cin >> n >> max_taitrong >> tang_ht)) return 0;
    yc_trong.assign(n + 1, false);

    while (true) {
        int loai_yc, ds1, ds2;
        cin >> loai_yc >> ds1 >> ds2;
        if (loai_yc == 0 && ds1 == 0 && ds2 == 0) break;

        if (loai_yc == 1) {
            yc.push_back({ds1, ds2});
        } else if (loai_yc == 2) {
            int tang_den = ds1;
            int tai_trong = ds2;

            if (tai_trong > max_taitrong) {
                cout << tang_ht << " " << huong_ht << " 3\n";
                return 0;
            }
            yc_trong[tang_den] = true;
        }
    }

    while (kt_con_yc_nao_khong()) {
        xu_ly_tai_tang();

        if (!co_yc_theo_chieu_ht() && kt_con_yc_nao_khong()) {
            huong_ht = -huong_ht;
            cout << tang_ht << " " << huong_ht << " 2\n";
        }

        if (kt_con_yc_nao_khong()) {
            tang_ht += huong_ht;
        }
    }

    return 0;
}
```
