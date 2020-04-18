# 指令之`ngFor`
依照慣例，接着在上一篇的項目增加功能。
## 添加一個`ToDo`接口
```
ng generate interface to-do
```
修改生成的文件如下：
```typescript
// src/app/to-do.ts
export interface ToDo {
  id: number; // 唯一標識
  content: string; // 內容
}
```
用作每一項的模型。
## 更新組件類
```typescript
// src/app/app.component.ts
import { Component } from '@angular/core';
import { ToDo } from './to-do';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'ng-app';
  value: string = '';
  list: ToDo[] = [];
  index: number = 0;

  onInput(text: string): void {
    this.value = text;
  }

  onAdd(text: string): void {
    if (text === '') {
      alert('不允許為空');
      return;
    }
    this.list.push({ id: this.index, content: text });
    this.index++;
    this.value = '';
  }
}
```
主要添加了一個`list`容器以裝載`ToDo`項，`onAdd`方法把輸入框輸入的`value`添加到新項中，並且檢測空值。
## 更新組件視圖
```html
<!-- src/app/app.component.html -->
<div>
  <input type="text" [value]="value" (input)="onInput($event.target.value)" />
  <button (click)="onAdd(value)">添加</button>
</div>

<ul *ngFor="let i of list">
  <li>
    {{i.content}}
  </li>
</ul>

<router-outlet></router-outlet>
```
與上篇相比，主要是增加了`ul`列表，其中有個特別的`*ngFor`語法，功能比較直接，採用類似`let of`的語法動態展開為多項`li`，這種語法叫做「指令」。