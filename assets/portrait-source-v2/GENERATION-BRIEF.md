# 滑动变祖 · 人像生成交接（4 张参考图 → 24 张渐变图）

## 结论

* 阶段参考图：**4 张**
* 计划关键图：**24 张**
* 已生成：**10 / 24**
* 分辨率与色彩：**1024 × 1024，RGB**
* 生成图目录：`assets/portrait-source-v2/`
* 阶段参考图目录：`assets/portrait-source-v2/references/`

本版方案改为：**用 4 张阶段锚点参考图，生成 24 张连续渐变的人像关键图**。
4 张参考图只作为**锚点输入**，24 张输出图才是最终用于前端滑动渐变序列的生产资产。

建议使用以下 4 张锚点参考图：

* `references/stage-00-0.000s.png`
* `references/stage-12-3.200s.png`
* `references/stage-24-6.400s.png`
* `references/stage-30-8.000s.png`

---

## 生成原则

1. **4 张参考图是锚点，不是最终交付图。**

   它们分别代表渐变序列中的关键阶段：`00`、`12`、`24`、`30`。

2. **24 张输出图必须形成连续渐变。**

   除锚点对应的输出图外，其余关键图都应位于相邻两个锚点之间，表现为平滑、自然、逐步增强的中间状态。

3. **人物身份必须全程一致。**

   必须严格保持同一位中国成年男性的身份特征，包括：脸型、眼镜、发型、肤色、年龄感、眼距、鼻子、嘴部和整体比例。

4. **变化只能来自强度渐变。**

   服装、表情、姿态、装饰和阶段感应随着强度逐步变化，但必须遵守相邻锚点之间的渐进关系，不能突然跳变，也不能提前借用后续更高阶段的元素。

5. **构图与镜头必须全序列统一。**

   所有 24 张图都必须保持统一机位、统一景别、统一光线方向、统一背景逻辑，确保前端切换时不跳帧、不抖动。

---

## 区间说明

24 张关键图分为 3 个渐变区间：

* **区间 A：`00 → 12`**
  使用 `references/stage-00-0.000s.png` 和 `references/stage-12-3.200s.png` 作为前后锚点。
* **区间 B：`12 → 24`**
  使用 `references/stage-12-3.200s.png` 和 `references/stage-24-6.400s.png` 作为前后锚点。
* **区间 C：`24 → 30`**
  使用 `references/stage-24-6.400s.png` 和 `references/stage-30-8.000s.png` 作为前后锚点。

规则如下：

* 当输出强度正好等于锚点强度（`00`、`12`、`24`、`30`）时，输出图应尽量贴近对应锚点参考图。
* 当输出强度位于两个锚点之间时，应生成自然过渡的中间状态。
* 越接近前锚点，就越接近前锚点的服装/表情/装饰状态；越接近后锚点，就越接近后锚点的状态。
* 所有中间图都必须看起来像**同一人、同一机位、同一组拍摄条件下的连续演变序列**。

---

## 公共提示词

下面这段作为每张图的公共提示词。把大括号变量替换为表格中对应内容；所有中间帧都必须根据前后锚点生成渐变结果，不能把某一张参考图机械复制为最终图。

```text
Use case: identity-preserve
Asset type: square key portrait for a continuous portrait evolution sequence

Primary request:
Using {LOWER_ANCHOR} as the lower-stage anchor reference and {UPPER_ANCHOR} as the upper-stage anchor reference, create the level {LEVEL} key portrait of the same Chinese adult man.

Preserve his identity exactly across the full sequence: facial structure, glasses, hairstyle, skin tone, age, eye spacing, nose, mouth and overall body proportions.

If {LEVEL} exactly matches an anchor level, the result must closely match that anchor's stage appearance.
If {LEVEL} falls between two anchors, generate a smooth intermediate stage between the two anchors. The appearance must progress gradually according to {INTERVAL_RULE}. Outfit, expression, posture, accessories and stage intensity should evolve naturally from the lower anchor toward the upper anchor, without abrupt jumps and without borrowing elements from a later interval.

Composition and camera:
Photorealistic 1:1 studio portrait, 1024×1024. Full centered half-body composition from the top of the head to the waist or slightly below the waist. The complete head, shoulders, upper arms and torso must remain inside the frame. Keep generous and balanced safety margins on the top, left and right. The head must be clearly smaller than a headshot and must not dominate the frame. Front-facing, eye-level camera, symmetrical posture, 85mm portrait-lens look, no perspective distortion.

Continuity:
Use {PREVIOUS_KEY} and {NEXT_KEY} only as continuity references. Keep eye line, head size, shoulder width, body scale, camera distance, background horizon and light direction consistent across the sequence. Changes from adjacent keys must be gradual and attributable only to increasing intensity. Do not change identity. Do not introduce an intermediate transition pose or motion blur.

Scene and lighting:
Clean full-frame studio background with a subtle neutral gradient appropriate to this stage. The background must fill the entire square evenly; do not bake a left-side blank layout or right-side placement into the image. Soft frontal key light, restrained rim light, realistic skin texture, natural fabric texture, high-end editorial photography.

Constraints:
No close-up crop. No cropped head, shoulders, arms or torso. No side pose. No extra person. No duplicated limbs or accessories. No text, logo, UI, frame, border or watermark. No motion blur. No transition frame. No abrupt stage jump. No mixed or conflicting accessories. Output only the final square image to {OUTPUT_PATH}.
```

---

## 24 张关键图状态表

| 顺序 | 强度 | 区间       | 状态  | 规范输出文件          | 下锚点参考                            | 上锚点参考                            | 渐变要求                    | 连续性参考                                  |
| -: | -: | -------- | --- | --------------- | -------------------------------- | -------------------------------- | ----------------------- | -------------------------------------- |
|  1 | 00 | A（00→12） | 待生成 | `stage-00.png`  | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 精确贴近 00 锚点              | 下一张 `level-01.png`                     |
|  2 | 01 | A（00→12） | 已生成 | `level-01.png`  | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 非常接近 00，开始轻微向 12 过渡     | 前一张 `stage-00.png`；下一张 `level-03.png`  |
|  3 | 03 | A（00→12） | 已生成 | `level-03.png`  | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 仍偏 00，但过渡感比 01 更明显      | 前一张 `level-01.png`；下一张 `level-04.png`  |
|  4 | 04 | A（00→12） | 已生成 | `level-04.png`  | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 处于 00→12 的早段中间态         | 前一张 `level-03.png`；下一张 `stage-06.png`  |
|  5 | 06 | A（00→12） | 已生成 | `stage-06.png`  | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 处于区间中段，前后特征应均衡          | 前一张 `level-04.png`；下一张 `level-07.png`  |
|  6 | 07 | A（00→12） | 已生成 | `level-07.png`  | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 略偏向 12，但仍明显属于中间态        | 前一张 `stage-06.png`；下一张 `level-09.png`  |
|  7 | 09 | A（00→12） | 已生成 | `level-09.png`  | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 明显接近 12，但不能直接跳到 12      | 前一张 `level-07.png`；下一张 `level-10.png`  |
|  8 | 10 | A（00→12） | 已生成 | `level-10.png`  | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 高度接近 12，仅保留少量中间过渡感      | 前一张 `level-09.png`；下一张 `stage-12.png`  |
|  9 | 12 | A/B 锚点   | 待生成 | `stage-12.png`  | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 精确贴近 12 锚点              | 前一张 `level-10.png`；下一张 `level-13.png`  |
| 10 | 13 | B（12→24） | 已生成 | `level-13.png`  | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 非常接近 12，开始向 24 过渡       | 前一张 `stage-12.png`；下一张 `level-14.png`  |
| 11 | 14 | B（12→24） | 已生成 | `level-14.png`  | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 早段中间态，比 13 更接近中段        | 前一张 `level-13.png`；下一张 `bridge-15.png` |
| 12 | 15 | B（12→24） | 已生成 | `bridge-15.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 桥接图，重点保证 14→16 过渡顺滑     | 前一张 `level-14.png`；下一张 `level-16.png`  |
| 13 | 16 | B（12→24） | 待生成 | `level-16.png`  | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 区间中段，中间态必须自然稳定          | 前一张 `bridge-15.png`；下一张 `level-17.png` |
| 14 | 17 | B（12→24） | 待生成 | `level-17.png`  | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 略偏向 24，增强渐变感            | 前一张 `level-16.png`；下一张 `stage-18.png`  |
| 15 | 18 | B（12→24） | 待生成 | `stage-18.png`  | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 处于偏后段，应明显比 16/17 更接近 24 | 前一张 `level-17.png`；下一张 `level-19.png`  |
| 16 | 19 | B（12→24） | 待生成 | `level-19.png`  | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 更接近 24，但仍保留中间态属性        | 前一张 `stage-18.png`；下一张 `level-21.png`  |
| 17 | 21 | B（12→24） | 待生成 | `level-21.png`  | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 高度接近 24，不可跳过 22 直接到 24  | 前一张 `level-19.png`；下一张 `level-22.png`  |
| 18 | 22 | B（12→24） | 待生成 | `level-22.png`  | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 极接近 24，只保留少量过渡差异        | 前一张 `level-21.png`；下一张 `stage-24.png`  |
| 19 | 24 | B/C 锚点   | 待生成 | `stage-24.png`  | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 精确贴近 24 锚点              | 前一张 `level-22.png`；下一张 `level-25.png`  |
| 20 | 25 | C（24→30） | 待生成 | `level-25.png`  | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 非常接近 24，开始向 30 过渡       | 前一张 `stage-24.png`；下一张 `bridge-27.png` |
| 21 | 27 | C（24→30） | 待生成 | `bridge-27.png` | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 桥接图，重点保证 25→28 过渡顺滑     | 前一张 `level-25.png`；下一张 `level-28.png`  |
| 22 | 28 | C（24→30） | 待生成 | `level-28.png`  | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 明显接近 30，但仍保留中间感         | 前一张 `bridge-27.png`；下一张 `level-29.png` |
| 23 | 29 | C（24→30） | 待生成 | `level-29.png`  | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 高度接近 30，不可直接复制 30       | 前一张 `level-28.png`；下一张 `stage-30.png`  |
| 24 | 30 | C（24→30） | 待生成 | `stage-30.png`  | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 精确贴近 30 锚点              | 前一张 `level-29.png`                     |

---

## 交付要求

1. 最终交付物为 **24 张 1024×1024 的正方形 RGB 人像图**。

2. 文件名必须严格使用表格中的“规范输出文件”。

3. 所有输出图必须放入：

   `assets/portrait-source-v2/`

4. 4 张锚点参考图放入：

   `assets/portrait-source-v2/references/`

5. 前端最终只使用这 24 张输出图，不直接使用 4 张参考图作为展示资产。

---

## 备注

* 本版方案的核心是：**4 张参考图只负责定义阶段锚点，24 张输出图负责定义完整渐变序列。**
* 锚点图之间的差异必须通过中间关键图平滑展开，不能出现跳帧、镜头漂移、人物比例漂移或阶段特征错位。
* 所有表格路径均相对于本文件所在目录。
