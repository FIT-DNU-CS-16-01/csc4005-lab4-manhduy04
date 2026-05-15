# CSC4005 Lab 4 Report – CRNN for UrbanSound8K

## 1. Thông tin sinh viên

- Họ tên:
- Mã sinh viên:
- Lớp:
- Link GitHub repo:
- Link W&B project:https://wandb.ai/duy19912745-dainam-vietnam/csc4005-lab4-urbansound8k-crnn?nw=nwuserduy19912745

## 2. Mục tiêu thí nghiệm

Mục tiêu của Lab 4 là xây dựng mô hình CRNN (Convolutional Recurrent Neural Network) để phân loại âm thanh trong bộ dữ liệu UrbanSound8K. Log-mel spectrogram được sử dụng vì nó biểu diễn tín hiệu theo cả thời gian và tần số, phù hợp với đặc trưng thính giác của con người. So với 1D-CNN ở Lab 3, CRNN kết hợp CNN để trích xuất đặc trưng cục bộ và RNN để học quan hệ theo thời gian, giúp cải thiện độ chính xác phân loại và khả năng tổng quát hóa.

## 3. Cấu hình dữ liệu

| Thành phần      | Giá trị             |
| --------------- | ------------------- |
| Dataset         | UrbanSound8K        |
| Số lớp          | 10                  |
| Train folds     | 1–8                 |
| Validation fold | 9                   |
| Test fold       | 10                  |
| Feature         | log-mel spectrogram |
| Sampling rate   | 16 kHz              |
| Duration        | 4 giây              |


## 4. Cấu hình mô hình

| Thành phần    | Giá trị                             |
| ------------- | ----------------------------------- |
| Model         | CRNN                                |
| CNN blocks    | 3                                   |
| RNN type      | GRU (baseline) / BiLSTM (extension) |
| Hidden size   | 128                                 |
| Dropout       | 0.3 – 0.4                           |
| Optimizer     | Adam                                |
| Learning rate | 0.001 hoặc 8e-4                     |
| Batch size    | 64                                  |
| Epochs        | 8–10                                |


## 5. Kết quả huấn luyện

Điền kết quả tốt nhất từ W&B hoặc `metrics.json`.

| Run | best_val_acc | test_acc | Ghi chú |
|---|---:|---:|---|
| logmel_crnn_gru_baseline | 0.7463 | 0.7551 | GRU baseline |
| extension_bilstm_crnn | 0.6348 | 0.6882 | BiLSTM mở rộng |

Nhận xét:

- Baseline GRU cho kết quả tốt hơn trong thử nghiệm này.
- Extension BiLSTM có độ chính xác thấp hơn, có thể do cần tuning thêm hoặc do overfitting với cấu hình hiện tại.

## 6. Learning curves

![alt text](<Screenshot 2026-05-15 092330.png>)

Nhận xét:

- Training loss giảm đều, cho thấy mô hình học được đặc trưng từ dữ liệu.
- Validation loss và validation accuracy cho thấy GRU baseline ổn định hơn so với BiLSTM extension.
- Early stopping với patience=6 được cấu hình để giảm overfitting khi validation không cải thiện.

## 7. Confusion matrix

![alt text](<Screenshot 2026-05-15 092421.png>)

Nhận xét:

- Lớp phân loại tốt: `gun_shot`, `jackhammer`, `air_conditioner`, `dog_bark`.
- Lớp dễ nhầm: `siren` thường bị nhầm, và các lớp tần số thấp như `air_conditioner`, `engine_idling`, `drilling` có thể bị trộn lẫn.
- Nguyên nhân: các tiếng ồn nền tần số thấp có phổ tương tự và khó phân biệt chỉ bằng log-mel mà không có augmentation hoặc đặc trưng temporal mạnh hơn.

## 8. So sánh với Lab 3 1D-CNN
| Tiêu chí                       | Lab 3: 1D-CNN  | Lab 4: CRNN  |
| ------------------------------ | -------------- | ------------ |
| Feature chính                  | MFCC / log-mel | log-mel      |
| Khả năng học pattern cục bộ    | Có             | Có           |
| Khả năng học quan hệ thời gian | Hạn chế        | Tốt hơn      |
| Test accuracy                  |  0.70          |  0.7551      |
| Nhận xét                       | Đơn giản       | Mạnh hơn     |

| Nhận xét | Mô hình đơn giản, chủ yếu học đặc trưng local | CRNN học thêm temporal, nên có tiềm năng mạnh hơn |
👉 Insight:

CNN chỉ học spatial
CRNN học thêm temporal → mạnh hơn rõ
## 9. Kết luận

CRNN đã cho kết quả tốt hơn so với 1D-CNN nhờ khả năng học đặc trưng không gian và quan hệ thời gian. Mô hình baseline GRU đạt test accuracy ~75.5%, trong khi BiLSTM hiện tại chưa cải thiện và cần tuning thêm. Kết quả cho thấy mô hình hoạt động ổn định nhưng vẫn có nguy cơ overfitting nếu train lâu và nếu không điều chỉnh hyperparameter. Hướng cải thiện: augmentation (noise, time stretch), thêm attention layer hoặc transformer audio model, và tuning learning rate / hidden dim.

## 10. Link minh chứng

- GitHub commit cuối:https://github.com/FIT-DNU-CS-16-01/csc4005-lab4-manhduy04
- W&B run baseline:https://wandb.ai/duy19912745-dainam-vietnam/csc4005-lab4-urbansound8k-crnn/runs/grhth72v?nw=nwuserduy19912745
- W&B run mở rộng:https://wandb.ai/duy19912745-dainam-vietnam/csc4005-lab4-urbansound8k-crnn/runs/4pb4hrdp?nw=nwuserduy19912745
