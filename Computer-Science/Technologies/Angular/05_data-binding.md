# Data binding

- `Data binding` is the process of pass data between the component class and the template.

## Property binding

- Property binding in Angular helps you set values for properties of HTML elements or directives. With property binding, you can do things such as toggle button functionality, set paths programmatically, and share values between components.

- Property binding is a one-way binding, that is, moves a value in one direction, from a component's property into a target element property.

```html
<img [src]="itemImageUrl" />
```

## Attribute, class, and style bindings

- Attribute binding in Angular helps you set values for attributes directly. With attribute binding, you can improve accessibility, style your application dynamically, and manage multiple CSS classes or styles simultaneously.

```html
<p [attr.attribute-you-are-targeting]="expression"></p>
```

### Binding ARIA attributes

- One of the primary use cases for attribute binding is to set ARIA attributes

```html
<!-- create and set an aria attribute for assistive technology -->
<button [attr.aria-label]="actionName">{{ actionName }}</button>
```

### Binding to the class attribute

- You can use class binding to add and remove CSS class names from an element's `class` attribute.

#### Binding to a single CSS class

- `[class.sale]="onSale"`

#### Binding to multiple CSS classes

- `[class]="classExpression"`

### Binding to the style attribute

- You can use style binding to set styles dynamically

- You can write a style property name in either `dash-case`, or `camelCase`

```html
<nav [style.background-color]="expression"></nav>

<nav [style.backgroundColor]="expression"></nav>
```

## Event binding

- Event binding lets you listen for and respond to user actions such as keystrokes, mouse movements, clicks, and touches.

### Binding to events

- To bind to an event you use the Angular event binding syntax. This syntax consists of a target event name within parentheses to the left of an equal sign, and a quoted template statement to the right.

```html
<button (click)="onSave()">Save</button>
```

- The event binding listens for the button's click events and calls the component's `onSave()` method whenever a click occurs.

![](https://angular.io/generated/images/guide/template-syntax/syntax-diagram.svg)

## Two-way binding

- Two-way binding gives components in your application a way to share data. Use two-way binding to listen for events and update values simultaneously between parent and child components.

```html
<app-sizer [(size)]="fontSizePx"></app-sizer>
```

### How two-way binding works

- For two-ways data binding to work, the `@Output()` property must use pattern, `inputChange`, where `input` is the name of the `@Input` property. For example, if the `@Input()` property is `size`, the `@Output()` property must be `sizeChange`.

- The following `sizerComponent` has a `size` value property and a `sizeChange` event. The `size` property is an `@Input()`, so data can flow into the `sizerComponent`. The `sizeChange` event is an `@Output()`, which lets data flow out of the `sizerComponent` to the parent component.

```ts
export class SizerComponent {
  @Input() size!: number | string;

  @Output() sizeChange = new EventEmitter<number>();

  dec() {
    this.resize(-1);
  }

  inc() {
    this.resize(+1);
  }

  resize(delta: number) {
    this.size = Math.min(40, Math.max(8, +this.size + delta));
    this.sizeChange.emit(this.size);
  }
}
```

```html
<div>
  <button (click)="dec()" title="smaller">-</button>
  <button (click)="inc()" title="bigger">+</button>
  <label [style.font-size.px]="size">FontSize: {{ size }}px</label>
</div>
```

```html
<app-sizer [(size)]="fontSizePx"></app-sizer>
<div [style.font-size.px]="fontSizePx">Resizable Text</div>
```

```html
<app-sizer [size]="fontSizePx" (sizeChange)="fontSizePx=$event"></app-sizer>
```

## References

- [Angular IO](https://angular.io)
- [Loiane Training](https://loiane.training)
