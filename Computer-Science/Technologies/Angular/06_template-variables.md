# Template variables

- Template variables help you use data from one part of a template in another part of the template.

- A template variable can refer to the following:
  - a DOM element whitin a template,
  - a directive,
  - an element,
  - [TemplateRef](https://angular.io/api/core/TemplateRef),
  - a `web component`

## Syntax

- In the template, use the hash symbol, `#`, to declare a template variable.

```html
<input #phone placeholder="phone number" />
```

- Refer to a template variable anywhere in the component's template.

```html
<input #phone placeholder="phone number" />

<button (click)="callPhone(phone.value)">Call</button>
```

## How Angular assigns values to template variables

- Angular assigns a template variable a value based on where you declare the variable:
  - If you declare the variable on a component, the variable refers to the component instance.
  - If you declare the variable on a standard HTMl tag, the variable refers to the element.
  - If you declare the variable on an `<ng-template>` element, the variable refers to a `TemplateRef` instace, which represents the template.
  - If the variable specifies a name on the right-hand side, such as `#var="ngModel"`, the variables refers to the directive or component on the element with a matching `exportAs` name.

### Using `NgForm` with template variables

- 