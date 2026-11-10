# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con, sedan, SUV, taxi và xe bán tải/cabin nhỏ. | Không gán xe buýt hoặc xe tải chở hàng vào lớp này. |
| `truck` | Xe tải chở hàng hoặc xe đầu kéo có thân xe/cabin lớn. | Xe bán tải hoặc cabin nhỏ vẫn là `car`. |
| `bus` | Xe buýt hoặc minibus được thiết kế chủ yếu để chở khách. | Không gán van chở người/chở hàng vào lớp này. |
| `van` | Xe van hoặc minivan chở hàng/chở người, có thân xe dạng hộp. | Không gán xe buýt lớn hoặc xe con thông thường vào lớp này. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: bao trọn phần vật thể nhìn thấy, không lấy thêm nền và không cắt mất phần nhìn thấy.
- Vật thể bị che khuất: vẫn gán nếu phần nhìn thấy đủ để nhận diện là một xe thuộc schema; box bao quanh phần xe nhìn thấy.
- Vật thể bị cắt bởi rìa ảnh: vẫn gán nếu phần nhìn thấy đủ để nhận diện; box chạm rìa ảnh và không kéo ra ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: chỉ gán khi có thể xác định đó là một xe thuộc bốn lớp; không gán vật thể không thể phân biệt đáng tin cậy.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001` | Cảnh đường ướt có nhiều xe ở các cự ly khác nhau; một số xe ở xa rất nhỏ, trong khi xe tiền cảnh bị nhòe chuyển động. | Gán `car` cho xe còn nhận diện được; box chỉ ôm phần xe quan sát được. Không đoán các mảnh quá nhỏ/mờ. | Kích thước nhỏ hoặc nhòe không tự động loại xe, nhưng phải còn đủ hình dáng để xác định là một lớp trong schema. |
| `drive_002` | Nhiều loại xe cùng xuất hiện và chồng lấn: xe buýt lớn, van/minivan và xe chở hàng ở rìa ảnh. | Tách từng xe thành box riêng; xe buýt chở khách là `bus`, van thân hộp là `van`, xe chở hàng/cabin lớn là `truck`, xe con là `car`. | Phân loại theo loại xe, không theo việc xe đang ở cùng làn hay bị che; không gộp các xe chồng lên nhau thành một box. |
| `drive_009` | Ùn tắc dưới mưa, nhiều xe buýt và xe tải/van bị che một phần bởi xe phía trước. | Gán các xe còn đủ dấu hiệu nhận diện, vẽ từng box quanh phần nhìn thấy; không vẽ phần bị che. | Đây là ca kiểm tra đồng thời che khuất, nhiều xe cùng lớp và phân biệt `bus` với `van`/`truck`. |
| `drive_014` | Xe cứu hộ dạng xe chở hàng ở gần mép dưới, bị cắt khung; phía sau có xe buýt và nhiều xe con. | Gán xe cứu hộ là `truck` nếu phần cabin/thùng còn đủ nhận diện; box chạm mép ảnh và chỉ bao phần nằm trong ảnh. | Xe bị cắt khung vẫn được gán khi nhận diện được; không kéo box ra ngoài ảnh và không gán người/xe máy. |
| `drive_034` | Xe cứu hộ ở tiền cảnh bị che/cắt một phần, cạnh nhiều xe con trong giao lộ. | Giữ box riêng cho xe cứu hộ và các xe con nhìn thấy; box của xe bị che chỉ bao phần nhìn thấy. | Hai xe chồng hình vẫn là hai vật thể; che khuất không phải lý do để bỏ qua nếu loại xe còn xác định được. |
| `drive_042` | Xe buýt lớn chạy cạnh xe tải chở hàng chất cao, cả hai đều bị nhòe do chuyển động. | Gán xe buýt là `bus`, xe chở hàng là `truck`; không gán xe máy/người đi bộ. | Dạng thân và mục đích sử dụng giúp phân biệt `bus` với `truck`; nhòe vừa phải không thay đổi lớp nếu vẫn nhận diện đáng tin cậy. |
