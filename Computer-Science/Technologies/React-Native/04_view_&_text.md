# View vs Text - A Summary

## View

- A `View` is your main core component if you need to group and structure content (provide a layout, like a div in web) or if you want to style something as a container.

- A View uses **flexbox** to organize its children.

- A View can hold many child components as is need and it also works with any kind of child component.

- If you need scrolling, you should consider using a `ScrollView`.

## Text

- You can nest other `Text` components into another Text.

- Unlike View, Text does **not use flexbox** for organizing its content. Instead, text inside of `Text` automatically fills a line as you would expect it and wraps into a new line if the text is too long for the available `Text` width.

- When adding styles to a `Text` (no matter if that happens via inline styles or a `StyleSheet` object), the styles will actually be shared with any nested `Text` components. This differs form the behavior of View (or actually any other component - `Text`is the exception).

## References

- [React Native The Practical Guide - Maximilian Schwarzmüller, Udemy](https://www.udemy.com/share/101Wau2@PUdKVGFKWlEIdUdKCnF3VBRu/)
