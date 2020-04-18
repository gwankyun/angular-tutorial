# 指令之`ngIf`
上篇添加新增`ToDo`功能，本篇則繼續添加刪除`ToDo`功能。
## 更新`ToDo`接口
```typescript
// src/app/to-do.ts
export interface ToDo {
  id: number; // 唯一標識
  content: string; // 內容
  selected: boolean; // 是否被選中
}
```
添加一個字段表示此項被選擇。
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
    this.list.push({ id: this.index, content: text, selected: false });
    this.index++;
    this.value = '';
  }

  onSelect(id: number): void {
    let idx = this.list.findIndex(i => i.selected);
    if (idx !== -1) {
      this.list[idx].selected = false;
    }
    this.list[id].selected = true;
  }

  onRemove(id: number): void {
    const idx = this.list.findIndex(i => i.id === id);
    if (idx !== -1) {
      this.list.splice(idx, 1);
    }
  }
}
```
添加了兩個方法：
- `onSelect`

此方法通過傳入`id`取消上一項的選擇狀態及更新當前項為選擇狀態。
- `onRemove`
此方法通過傳入`id`刪除項。
## 更新組件視圖
```html
<!-- src/app/app.component.html -->
<div>
  <input type="text" [value]="value" (input)="onInput($event.target.value)" />
  <button (click)="onAdd(value)">添加</button>
</div>

<ul *ngFor="let i of list">
  <li>
    <div class="toDo">
      <input type="radio" name="current" (input)="onSelect(i.id)" [checked]="i.selected" />
      <label for="current">
        {{i.content}}
      </label>
    </div>
    <div *ngIf="i.selected">
      <button (click)="onRemove(i.id)">刪除</button>
    </div>
  </li>
</ul>

<router-outlet></router-outlet>
```
`radio`組件及綁定的`input`事件更新`selected`字段不難理解。

特別是`*ngIf`語法，這也是指令，Angular所有指令以`*`開頭，功能是根據傳入條件顯示組件，此處作用是衹在選中的項顯示刪除按鍵。
## 更新組件樣式
```css
/* src/app/app.component.css */
.toDo {
  float: left; 
}
```
作用是讓`radio`和`label`同行顯示。