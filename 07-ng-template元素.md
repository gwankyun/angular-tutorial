# ng-template元素
目前項目已有增與刪功能，下面將會添加「修」功能。
## to-do組件類
```typescript
import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';
import { ToDo } from '../to-do';

@Component({
  selector: 'app-to-do',
  templateUrl: './to-do.component.html',
  styleUrls: ['./to-do.component.css']
})
export class ToDoComponent implements OnInit {
  @Input() value: ToDo;
  @Output() select = new EventEmitter<number>();
  @Output() remove = new EventEmitter<number>();
  @Output() update = new EventEmitter<ToDo>();

  constructor() { }

  ngOnInit(): void {
  }

  onSelect(id: number): void {
    this.select.emit(id);
  }

  onRemove(id: number): void {
    this.remove.emit(id);
  }

  onUpdate(id: number, content: string): void {
    this.update.emit({id: id, content: content, selected: this.value.selected});
  }
}
```
添加了一個`update`事件發射器及相應的方法。
## to-do組件視圖
```html
<div class="toDo">
  <input type="radio" name="current" (input)="onSelect(value.id)" [checked]="value.selected" />
  <div *ngIf="value.selected; then selected else unselected">
  </div>
  <ng-template #selected>
    <input type="text" [value]="value.content" (input)="onUpdate(value.id, $event.target.value)" />
    <button (click)="onRemove(value.id)">刪除</button>
  </ng-template>
  <ng-template #unselected>
    <label for="current">
      {{value.content}}
    </label>
  </ng-template>
</div>
```
此處出現了一個`<ng-template>`新元素及`*ngIf`的裡面的新語法，意思是根據條件選擇顯示`selected`或者`unselected`模板，`#`是語法的一部分，未被選擇顯示`label`被選擇就顯示`input`，並且將更新的值發射上去。
## to-do服務類
```typescript
import { Injectable } from '@angular/core';
import { ToDo } from './to-do';

@Injectable({
  providedIn: 'root'
})
export class ToDoService {
  list: ToDo[] = [];
  index: number = 0;

  constructor() { }

  getList(): ToDo[] {
    return this.list;
  }

  add(text: string): string {
    if (text === '') {
      return '';
    }
    this.list.push({ id: this.index, content: text, selected: false });
    this.index++;
    return '';
  }

  remove(id: number): void {
    const idx = this.list.findIndex(i => i.id === id);
    if (idx !== -1) {
      this.list.splice(idx, 1);
    }
  }

  select(id: number): void {
    let idx = this.list.findIndex(i => i.selected);
    if (idx !== -1) {
      this.list[idx].selected = false;
    }
    idx = this.list.findIndex(i => i.id === id);
    if (idx !== -1) {
      this.list[idx].selected = true;
    }
  }

  update(toDo: ToDo) {
    const idx = this.list.findIndex(i => i.id === toDo.id);
    if (idx !== -1) {
      this.list[idx] = toDo;
    }
  }
}
```
添加了一個`update`方法來更新某項。
## app組件類
```typescript
import { Component, ChangeDetectionStrategy } from '@angular/core';
import { ToDo } from './to-do';
import { ToDoService } from './to-do.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'ng-app';
  value: string = '';
  list: ToDo[] = [];

  constructor(public toDoService: ToDoService) {
    this.list = toDoService.getList();
  }

  onInput(text: string): void {
    this.value = text;
  }

  onAdd(text: string): void {
    if (text === '') {
      alert('不允許為空');
      return;
    }
    this.value = this.toDoService.add(text);
  }

  onSelect(id: number): void {
    this.toDoService.select(id);
  }

  onRemove(id: number): void {
    this.toDoService.remove(id);
  }

  onUpdate(toDo: ToDo): void {
    this.toDoService.update(toDo);
  }
}
```
此裡很簡單地添加了個`onUpdate`方法來調用服務方法。
## app組件視圖
```html
<div>
  <input type="text" [value]="value" (input)="onInput($event.target.value)" />
  <button (click)="onAdd(value)">添加</button>
</div>

<ul *ngFor="let i of list">
  <li>
    <app-to-do [value]="i" (select)="onSelect(i.id)" (remove)="onRemove(i.id)" (update)="onUpdate($event)"></app-to-do>
  </li>
</ul>

<router-outlet></router-outlet>
```
添加了對新事件`update`的使用。