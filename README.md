
## 快速开始


```shell
conda create -n your_env_name python=3.10
conda activate your_env_name
pip install -r requirements.txt
```





### 数据集准备




在数据集上训练需要将其组织为 COCO 格式。请按照以下步骤准备你的数据集：

1. **将 `remap_mscoco_category` 设置为 `False`:**

    这可以防止类别 ID 自动映射以匹配 MSCOCO 类别。

    ```yaml
    remap_mscoco_category: False
    ```

2. **组织图像：**

    按以下结构组织你的数据集目录：

    ```shell
    dataset/
    ├── images/
    │   ├── train/
    │   │   ├── image1.jpg
    │   │   ├── image2.jpg
    │   │   └── ...
    │   ├── val/
    │   │   ├── image1.jpg
    │   │   ├── image2.jpg
    │   │   └── ...
    └── annotations/
        ├── instances_train.json
        ├── instances_val.json
        └── ...
    ```

    - **`images/train/`**: 包含所有训练图像。
    - **`images/val/`**: 包含所有验证图像。
    - **`annotations/`**: 包含 COCO 格式的注释文件。

3. **将注释转换为 COCO 格式：**

    如果你的注释尚未为 COCO 格式，你需要进行转换。你可以参考以下 Python 脚本或使用现有工具：

    ```python
    import json

    def convert_to_coco(input_annotations, output_annotations):
        # Implement conversion logic here
        pass

    if __name__ == "__main__":
        convert_to_coco('path/to/your_annotations.json', 'dataset/annotations/instances_train.json')
    ```

4. **更新配置文件：**

    修改你的 [dfine_hgnetv2_n_elc.yml](./configs/dataset/custom_detection.yml)。

    ```yaml
    task: detection

    evaluator:
      type: CocoEvaluator
      iou_types: ['bbox', ]

    num_classes: 1 # your dataset classes
    remap_mscoco_category: False

    train_dataloader:
      type: DataLoader
      dataset:
        type: CocoDetection
        img_folder: /data/yourdataset/train
        ann_file: /data/yourdataset/train/train.json
        return_masks: False
        transforms:
          type: Compose
          ops: ~
      shuffle: True
      num_workers: 4
      drop_last: True
      collate_fn:
        type: BatchImageCollateFunction

    val_dataloader:
      type: DataLoader
      dataset:
        type: CocoDetection
        img_folder: /data/yourdataset/val
        ann_file: /data/yourdataset/val/ann.json
        return_masks: False
        transforms:
          type: Compose
          ops: ~
      shuffle: False
      num_workers: 4
      drop_last: False
      collate_fn:
        type: BatchImageCollateFunction
    ```

### 训练命令
```
CUDA_VISIBLE_DEVICES=<显卡id> python train.py -c <yml的路径> --seed=0
```



## 工具

<details>
<summary> 推理（可视化） </summary>


1. 设置
```shell
pip install -r tools/inference/requirements.txt
export model=l  # n s m l x
```


<!-- <summary>5. Inference </summary> -->
2. 推理 (torch)

```shell
python tools/inference/torch_inf.py -c configs/dfine/dfine_hgnetv2_n_elc.yml -r model.pth --input image.jpg --device cuda:0
```
</details>

<details>
<summary> 基准测试  </summary>

1. 设置
```shell
pip install -r tools/benchmark/requirements.txt
export model=l  # n s m l x
```

<!-- <summary>6. Benchmark </summary> -->
2. 模型 FLOPs、MACs、参数量
```shell
python tools/benchmark/get_info.py -c configs/dfine/dfine_hgnetv2_n_elc.yml
```







