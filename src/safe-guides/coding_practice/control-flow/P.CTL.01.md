## P.CTL.01 不要过度使用迭代器

**【描述】**

迭代器虽然是 Rust 中比较推崇的方式，但也没必要过度使用它。总之，如果使用迭代器让代码太复杂，就考虑换个非迭代器的方式实现吧。

**【反例】**

创建一个 Matrix变换的函数，但是这种迭代器的方式，代码可读性相比于命令式更困难。

```rust
// From : https://adventures.michaelfbryan.com/posts/rust-best-practices/bad-habits/#overusing-iterators
pub fn functional_blur(input: &Matrix) -> Matrix {
    assert!(input.width >= 3);
    assert!(input.height >= 3);

    // Stash away the top and bottom rows so they can be
    // directly copied across later
    let mut rows = input.rows();
    let first_row = rows.next().unwrap();
    let last_row = rows.next_back().unwrap();

    let top_row = input.rows();
    let middle_row = input.rows().skip(1);
    let bottom_row = input.rows().skip(2);

    let blurred_elements = top_row
        .zip(middle_row)
        .zip(bottom_row)
        .flat_map(|((top, middle), bottom)| blur_rows(top, middle, bottom));

    let elements: Vec<f32> = first_row
        .iter()
        .copied()
        .chain(blurred_elements)
        .chain(last_row.iter().copied())
        .collect();

    Matrix::new_row_major(elements, input.width, input.height)
}

fn blur_rows<'a>(
    top_row: &'a [f32],
    middle_row: &'a [f32],
    bottom_row: &'a [f32],
) -> impl Iterator<Item = f32> + 'a {
    // stash away the left-most and right-most elements so they can be copied across directly.
    let &first = middle_row.first().unwrap();
    let &last = middle_row.last().unwrap();

    // Get the top, middle, and bottom row of our 3x3 sub-matrix so they can be
    // averaged.
    let top_window = top_row.windows(3);
    let middle_window = middle_row.windows(3);
    let bottom_window = bottom_row.windows(3);

    // slide the 3x3 window across our middle row so we can get the average
    // of everything except the left-most and right-most elements.
    let averages = top_window
        .zip(middle_window)
        .zip(bottom_window)
        .map(|((top, middle), bottom)| top.iter().chain(middle).chain(bottom).sum::<f32>() / 9.0);

    std::iter::once(first)
        .chain(averages)
        .chain(std::iter::once(last))
}
```

**【正例】**

创建一个 Matrix变换的函数，使用命令式风格，代码功能比较明确，更加直观。

```rust
// From: https://adventures.michaelfbryan.com/posts/rust-best-practices/bad-habits/#overusing-iterators
pub fn imperative_blur(input: &Matrix) -> Matrix {
    assert!(input.width >= 3);
    assert!(input.height >= 3);

    // allocate our output matrix, copying from the input so
    // we don't need to worry about the edge cases.
    let mut output = input.clone();

    for y in 1..(input.height - 1) {
        for x in 1..(input.width - 1) {
            let mut pixel_value = 0.0;

            pixel_value += input[[x - 1, y - 1]];
            pixel_value += input[[x, y - 1]];
            pixel_value += input[[x + 1, y - 1]];

            pixel_value += input[[x - 1, y]];
            pixel_value += input[[x, y]];
            pixel_value += input[[x + 1, y]];

            pixel_value += input[[x - 1, y + 1]];
            pixel_value += input[[x, y + 1]];
            pixel_value += input[[x + 1, y + 1]];

            output[[x, y]] = pixel_value / 9.0;
        }
    }

    output
}
```



