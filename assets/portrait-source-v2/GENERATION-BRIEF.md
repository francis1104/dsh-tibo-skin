# 滑动变祖 · 人像生成交接（4 张参考图 → 24 张渐变图）

> 适用分支：`chore/update-portrait-references`
>
> 本文档以 `assets/portrait-source-v2/references/` 中的阶段参考图为视觉事实来源。若文字描述与参考图冲突，**始终以参考图为准**。

## 结论

* 阶段参考图：**4 张**
* 计划关键图：**24 张**
* 已生成：**22 / 24**
* 最终输出规格：**1024 × 1024，RGB，PNG**
* 生成图目录：`assets/portrait-source-v2/`
* 阶段参考图目录：`assets/portrait-source-v2/references/`
* 已归档的 12 张新增图片均为 **1024 × 1536，RGB，PNG**；已按档位归档，最终交付前仍需按生产规格重新构图/扩图为 1024×1024。

本版方案使用 **4 张阶段锚点参考图** 定义完整演变轨迹，再生成 **24 张连续渐变的人像关键图**。

4 张参考图只作为**锚点输入**；24 张输出图才是最终用于前端滑动渐变序列的生产资产。

建议使用以下 4 张锚点参考图：

* `references/stage-00-0.000s.png`
* `references/stage-12-3.200s.png`
* `references/stage-24-6.400s.png`
* `references/stage-30-8.000s.png`

---

## 最高优先级规则：参考图优先

1. **参考图是人物身份、服装、表情、姿态、装饰和阶段视觉的唯一权威来源。**

   不要根据文字提示自行补充参考图中不存在的人物特征或道具。

2. **不要硬编码族裔、眼镜或其他未在参考图中稳定出现的身份特征。**

   例如：如果当前锚点中的人物没有眼镜，就不要因为提示词中的旧描述而添加眼镜。

3. **相邻锚点之间只允许做渐进插值。**

   中间图只能在当前区间的前后两个锚点之间变化，不得提前借用下一区间的高阶段元素。

4. **锚点对应输出必须贴近锚点，但最终构图规格仍以生产要求为准。**

   如果参考图是竖版、非 1:1 或主体占比不符合最终规格，必须通过**重新构图 / 扩图（outpainting）**完成 1024×1024 输出，不能简单裁切、拉伸或压缩。

5. **阶段内可见文字属于道具的一部分时允许保留。**

   全局仍禁止额外文字、Logo、UI 和水印；但如果权威参考图中的阶段道具本身带有可见文字，应保留该道具文字。例如 `stage-24-6.400s.png` 中右手金币/徽章上的 `RESET` 应作为阶段道具保留，且不得额外生成其他文字。

---

## 生成原则

1. **4 张参考图是锚点，不是最终交付图。**

   它们分别代表渐变序列中的关键阶段：`00`、`12`、`24`、`30`。

2. **24 张输出图必须形成连续渐变。**

   除锚点对应的输出图外，其余关键图都应位于相邻两个锚点之间，表现为平滑、自然、逐步增强的中间状态。

3. **人物身份必须全程一致。**

   必须严格保持参考图中同一位成年男性的可见身份特征，包括脸型、发型、肤色、年龄感、眼距、鼻子、嘴部、下颌线和整体比例。

   只保留参考图中实际存在的特征；不要新增眼镜、胡须、首饰、头饰或其他身份特征。

4. **变化只能来自强度渐变。**

   服装、表情、姿态、装饰、光效和阶段感可以随着强度逐步变化，但必须遵守相邻锚点之间的渐进关系，不能突然跳变。

5. **构图与镜头必须全序列统一。**

   所有 24 张图都必须保持统一机位、统一景别、统一人物尺度、统一光线方向和统一背景逻辑，确保前端切换时不跳帧、不抖动。

6. **最终输出始终是正方形生产图。**

   源参考图可以是竖版；最终图必须重构为 1:1。不得为了塞进方形画布而裁掉头顶、肩膀、手臂、手持道具或躯干。

---

## 锚点视觉解释

### Stage 12

`references/stage-12-3.200s.png` 是 `12` 阶段的权威参考。

生成 `stage-12.png` 时：

* 保持参考图中的同一人物身份和面部结构。
* 保持平静、克制的阶段表情与红金/米色长袍造型。
* 不要添加参考图中没有的眼镜、光环、金币、`RESET` 道具或其他高阶段装饰。
* 参考图虽然是竖版，但最终必须重新构图为 1024×1024 的完整半身像。
* 不得简单居中裁成方图导致头部、肩部、手臂或躯干被截断。

### Stage 24

`references/stage-24-6.400s.png` 是 `24` 阶段的权威参考。

生成 `stage-24.png` 时：

* 保持参考图中的同一人物身份、微笑表情、红金华服和整体姿态。
* 保留阶段性金色光环、放射状金光和云雾背景。
* 保留左手的多叠金币。
* 保留右手的大型金币/徽章及其可见文字 `RESET`。
* `RESET` 是该阶段道具的一部分，是“禁止额外文字”规则的明确例外。
* 必须保证完整光环、双手、金币堆、`RESET` 道具和上半身均在 1024×1024 画面内。
* 不得因为方形构图而裁掉道具或手部。

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
* 越接近前锚点，就越接近前锚点的服装、表情、姿态、装饰和光效状态。
* 越接近后锚点，就越接近后锚点的状态。
* 所有中间图都必须看起来像**同一人、同一机位、同一组拍摄条件下的连续演变序列**。
* 某个阶段元素只有在相邻锚点关系允许时才能逐步出现；不得突然出现，也不得从未来区间提前借用。

---

## 公共提示词

下面这段作为每张图的公共提示词。

把大括号变量替换为表格中对应内容。所有中间帧都必须根据前后锚点生成渐变结果，不能机械复制某一张参考图。

```text
Use case: identity-preserve
Asset type: square key portrait for a continuous portrait evolution sequence

Source-of-truth rule:
The supplied anchor images are the authoritative visual source for identity, clothing, expression, pose, accessories, lighting and stage-specific details. If any written instruction conflicts with what is visibly present in the authoritative anchors, follow the anchors. Do not invent glasses, facial hair, jewelry, ethnicity-specific changes, accessories or costume details that are not supported by the relevant anchors.

Primary request:
Using {LOWER_ANCHOR} as the lower-stage anchor reference and {UPPER_ANCHOR} as the upper-stage anchor reference, create the level {LEVEL} key portrait of exactly the same adult man shown in the anchors.

Preserve the visible identity exactly across the full sequence: facial structure, hairstyle, skin tone, apparent age, eye spacing, nose, mouth, jawline and overall body proportions.

If {LEVEL} exactly matches an anchor level, the result must closely match that anchor's visible stage appearance while being recomposed to the required final square production framing.

If {LEVEL} falls between two anchors, generate a smooth intermediate stage between those two anchors only. The appearance must progress gradually according to {INTERVAL_RULE}. Outfit, expression, posture, accessories, background effects and stage intensity should evolve naturally from the lower anchor toward the upper anchor, without abrupt jumps and without borrowing elements from a later interval.

Composition and camera:
Photorealistic 1:1 portrait, final output exactly 1024×1024 RGB PNG. Full centered half-body composition from the top of the head to the waist or slightly below the waist. Keep the complete head, shoulders, upper arms, hands when visible or stage-relevant, stage-specific props, and torso inside the frame.

If the authoritative source image is portrait-oriented or otherwise not square, recompose and outpaint naturally into the square frame. Do not simply center-crop, stretch, squeeze or distort the source image.

Keep generous and balanced safety margins on the top, left and right. The head must be clearly smaller than a headshot and must not dominate the frame. Front-facing, eye-level camera, symmetrical or anchor-consistent posture, approximately 85mm portrait-lens look, no wide-angle perspective distortion.

Continuity:
Use {PREVIOUS_KEY} and {NEXT_KEY} only as continuity references. Keep eye line, head size, shoulder width, body scale, camera distance, background horizon and primary light direction consistent across the sequence. Changes from adjacent keys must be gradual and attributable only to increasing intensity. Do not change identity. Do not introduce a transition pose, motion blur or frame-blending artifact.

Scene and lighting:
Preserve the stage logic defined by the two anchors. Keep realistic skin texture, natural fabric texture and high-end editorial photographic detail. Background and lighting may intensify gradually when supported by the upper anchor, but must not jump ahead of the current level.

Text and prop rule:
Do not add text, logos, UI, frames, borders or watermarks. Exception: if visible text is physically part of a stage-specific prop in the authoritative anchor, preserve only that prop text when the current stage should contain that prop. Do not add any unrelated text.

Constraints:
No close-up crop.
No cropped head, shoulders, arms, hands when stage-relevant, props or torso.
No side pose unless explicitly shown by the authoritative anchor.
No extra person.
No duplicated limbs, fingers, coins or accessories.
No abrupt stage jump.
No mixed or conflicting accessories.
No invented glasses or accessories unsupported by the anchors.
No motion blur.
No transition frame.
No frame blending.
Output only the final square image to {OUTPUT_PATH}.
```

---

## 锚点输出专用补充提示

### `stage-12.png`

```text
Anchor target: level 12.

Use references/stage-12-3.200s.png as the authoritative visual source for this output.

Preserve the same visible identity, hairstyle, facial proportions, calm expression, red-and-gold outer robe, cream inner robe and restrained warm studio atmosphere shown in the reference.

Do not add glasses if they are not visible in the reference.
Do not add a halo.
Do not add coins.
Do not add a RESET token.
Do not import any visual element that belongs to level 24 or later.

The source reference is portrait-oriented. Recompose/outpaint it into a natural 1024×1024 half-body portrait instead of cropping it into a square.

Keep the entire head, shoulders, upper arms and torso inside the frame with generous safety margins.
```

### `stage-24.png`

```text
Anchor target: level 24.

Use references/stage-24-6.400s.png as the authoritative visual source for this output.

Preserve the same visible identity, hairstyle, facial proportions, smile, ornate cream-and-gold inner garment, richly embroidered deep-red outer robe, circular golden halo, radiant golden backlight, cloud-like luminous background, stacked coins in the left hand, and the large gold RESET token in the right hand.

The word RESET is intentional stage-prop text and must be preserved on that token. Do not generate any other text.

The source reference is portrait-oriented. Recompose/outpaint it into a natural 1024×1024 half-body portrait instead of cropping it into a square.

Keep the complete halo, head, shoulders, both arms, both hands, all important coin stacks, the RESET token and torso fully inside the frame with generous safety margins.

Do not duplicate coins, fingers, hands or tokens.
Do not crop the halo or RESET token.
```

---

## 24 张关键图状态表

| 顺序 | 强度 | 区间 | 状态 | 规范输出文件 | 下锚点参考 | 上锚点参考 | 渐变要求 | 连续性参考 |
|---:|---:|---|---|---|---|---|---|---|
| 1 | 00 | A（00→12） | 待生成 | `stage-00.png` | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 精确贴近 00 锚点 | 下一张 `level-01.png` |
| 2 | 01 | A（00→12） | 已生成 | `level-01.png` | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 非常接近 00，开始轻微向 12 过渡 | 前一张 `stage-00.png`；下一张 `level-03.png` |
| 3 | 03 | A（00→12） | 已生成 | `level-03.png` | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 仍偏 00，但过渡感比 01 更明显 | 前一张 `level-01.png`；下一张 `level-04.png` |
| 4 | 04 | A（00→12） | 已生成 | `level-04.png` | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 处于 00→12 的早段中间态 | 前一张 `level-03.png`；下一张 `stage-06.png` |
| 5 | 06 | A（00→12） | 已生成 | `stage-06.png` | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 处于区间中段，前后特征应均衡 | 前一张 `level-04.png`；下一张 `level-07.png` |
| 6 | 07 | A（00→12） | 已生成 | `level-07.png` | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 略偏向 12，但仍明显属于中间态 | 前一张 `stage-06.png`；下一张 `level-09.png` |
| 7 | 09 | A（00→12） | 已生成 | `level-09.png` | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 明显接近 12，但不能直接跳到 12 | 前一张 `level-07.png`；下一张 `level-10.png` |
| 8 | 10 | A（00→12） | 已生成 | `level-10.png` | `references/stage-00-0.000s.png` | `references/stage-12-3.200s.png` | 高度接近 12，仅保留少量中间过渡感 | 前一张 `level-09.png`；下一张 `stage-12.png` |
| 9 | 12 | A/B 锚点 | 待生成 | `stage-12.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 精确贴近 12 锚点；不得加入 24 阶段光环、金币或 RESET 道具 | 前一张 `level-10.png`；下一张 `level-13.png` |
| 10 | 13 | B（12→24） | 已生成 | `level-13.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 非常接近 12，开始向 24 过渡 | 前一张 `stage-12.png`；下一张 `level-14.png` |
| 11 | 14 | B（12→24） | 已生成 | `level-14.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 早段中间态，比 13 更接近中段 | 前一张 `level-13.png`；下一张 `bridge-15.png` |
| 12 | 15 | B（12→24） | 已生成 | `bridge-15.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 桥接图，重点保证 14→16 过渡顺滑 | 前一张 `level-14.png`；下一张 `level-16.png` |
| 13 | 16 | B（12→24） | 已生成 | `level-16.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 区间中段，中间态必须自然稳定 | 前一张 `bridge-15.png`；下一张 `level-17.png` |
| 14 | 17 | B（12→24） | 已生成 | `level-17.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 略偏向 24，增强渐变感 | 前一张 `level-16.png`；下一张 `stage-18.png` |
| 15 | 18 | B（12→24） | 已生成 | `stage-18.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 处于偏后段，应明显比 16/17 更接近 24 | 前一张 `level-17.png`；下一张 `level-19.png` |
| 16 | 19 | B（12→24） | 已生成 | `level-19.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 更接近 24，但仍保留中间态属性 | 前一张 `stage-18.png`；下一张 `level-21.png` |
| 17 | 21 | B（12→24） | 已生成 | `level-21.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 高度接近 24，不可跳过 22 直接到 24 | 前一张 `level-19.png`；下一张 `level-22.png` |
| 18 | 22 | B（12→24） | 已生成 | `level-22.png` | `references/stage-12-3.200s.png` | `references/stage-24-6.400s.png` | 极接近 24，只保留少量过渡差异 | 前一张 `level-21.png`；下一张 `stage-24.png` |
| 19 | 24 | B/C 锚点 | 已生成 | `stage-24.png` | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 精确贴近 24 锚点；完整保留光环、金币和 RESET 道具 | 前一张 `level-22.png`；下一张 `level-25.png` |
| 20 | 25 | C（24→30） | 已生成 | `level-25.png` | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 非常接近 24，开始向 30 过渡 | 前一张 `stage-24.png`；下一张 `bridge-27.png` |
| 21 | 27 | C（24→30） | 已生成 | `bridge-27.png` | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 桥接图，重点保证 25→28 过渡顺滑 | 前一张 `level-25.png`；下一张 `level-28.png` |
| 22 | 28 | C（24→30） | 已生成 | `level-28.png` | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 明显接近 30，但仍保留中间感 | 前一张 `bridge-27.png`；下一张 `level-29.png` |
| 23 | 29 | C（24→30） | 已生成 | `level-29.png` | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 高度接近 30，不可直接复制 30 | 前一张 `level-28.png`；下一张 `stage-30.png` |
| 24 | 30 | C（24→30） | 已生成 | `stage-30.png` | `references/stage-24-6.400s.png` | `references/stage-30-8.000s.png` | 精确贴近 30 锚点 | 前一张 `level-29.png` |

---

## 交付要求

1. 最终交付物为 **24 张 1024×1024 的正方形 RGB PNG 人像图**。

2. 文件名必须严格使用表格中的“规范输出文件”。

3. 所有输出图必须放入：

   `assets/portrait-source-v2/`

4. 4 张锚点参考图放入：

   `assets/portrait-source-v2/references/`

5. 前端最终只使用这 24 张输出图，不直接使用 4 张参考图作为展示资产。

6. 非方形参考图必须通过重新构图/扩图转为最终正方形生产图；禁止通过简单裁切造成主体或道具缺失。

7. 每次新生成一张图后，应同时检查：
   * 身份一致性；
   * 相邻阶段过渡；
   * 人物尺度与眼线；
   * 手部和道具完整性；
   * 是否误引入未来阶段元素；
   * 是否生成了不应出现的文字；
   * 锚点阶段的专属道具是否完整保留。

---

## 备注

* 本版方案的核心是：**4 张参考图负责定义阶段锚点，24 张输出图负责定义完整渐变序列。**
* 锚点图之间的差异必须通过中间关键图平滑展开，不能出现跳帧、镜头漂移、人物比例漂移或阶段特征错位。
* 人物身份必须来自参考图本身，不再使用“固定族裔 + 固定眼镜”等与实际锚点可能冲突的硬编码描述。
* `stage-12` 与 `stage-24` 的具体视觉约束已按当前权威参考图补充。
* 所有表格路径均相对于本文件所在目录。
