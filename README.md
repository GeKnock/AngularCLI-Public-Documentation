# Краткая документация Angular CLI (по книге)

## Глава 1. Основные моменты. Создание и подготовка проекта Angular

### Создание проекта Angular

```bat
ng new <имя проекта>
```

<i><u>Примечание:</u> <b><имя проекта></b> - новая папка с приложением Angular</i>

### Установка плагинов из package.json

```bat
npm install
```

### Запуск сервера / Запуск сервера на конкретном порту

```bat
npm serve --open
```

```bat
npm serve --port 3300 --open
```

### Подключение к index.html встроенного модуля Bootstrap

```html

<link href="node_modules/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet"/>
```

<blockquote style="color: rgba(255,162,162,0.8)">! В приложениях Angular данные отделяются от способа их представления для пользователя. Это один из ключевых принципов паттерна MVC</blockquote>

Приложения Angular обычно пишутся на TypeScript.TypeScript является надмножеством JavaScript, но одно из его главных
преимуществ заключается в том, что он позволяет писать код с использованием новейшей спецификации языка JavaScript,
часть возможностей которого не поддерживается некоторыми браузерами, способными к выполнению приложений Angular. Также
важнейших особенностей TypeScript заключается в том, что вы можете писать «нормальный» код JavaScript так, как если бы
вы писали его непосредственно для браузера.
Но полезность TypeScript также в значительной мере обусловлена и тем, что TypeScript берет код, использующий новейшие
возможности языка JavaScript, в код, который будет работать везде — даже в браузерах, не поддерживающих эту
функциональность.

### Модель данных (MVC)

Только JavaScript:

```typescript
var model = {
    user: "Adam",
    items: [{action: "Buy Flowers", done: false},
        {action: "Get Shoes", done: false},
        {action: "Collect Tickets", done: true},
        {action: "Call Joe", done: false}]
};
```

С использованием EC6:

```typescript
export class Model {
    user;
    items;

    constructor() {
        this.user = "Adam";
        this.items = [new TodoItem("Buy Flowers", false),
            new TodoItem("Get Shoes", false),
            new TodoItem("Collect Tickets", false),
            new TodoItem("Call Joe", false)]
    }
}

export class TodoItem {
    action;
    done;

    constructor(action, done) {
        this.action = action;
        this.done = done;
    }
}
```

<blockquote style="color: rgba(255,162,162,0.8)">! Ключевое слово <b>export</b> относится к работе с модулями JavaScript. При использовании модулей каждый файл TypeScript или JavaScript считается автономным блоком функциональности, а ключевое слово export идентифицирует данные и типы, которые должны использоваться в других местах приложения. Модули JavaScript используются для управления зависимостями между файлами проекта, а также для того, чтобы разработчику не приходилось вручную управлять сложным набором элементов script в файле HTML.</blockquote>

### Привязка данных в шаблоне (Шаблонизатор)

```angular17html
{{ content }}
```

> Пример: ```<h3 class="bg-primary p-a-1">{{getName()}}'s To Do List</h3>```

### Компонент Angular

Пример компонента

```typescript
import {Component} from "@angular/core";
import {Model} from "./model";

@Component({
    selector: "todo-app",
    templateUrl: "app/app.component.html"
})
export class AppComponent {
    model = new Model();

    getName() {
        return this.model.user;
    }
}
```

### Корневой модуль

В приложениях Angular должен присутствовать модуль. В разработке приложений Angular встречаются модули двух видов.
Модуль JavaScript — файл с функциональностью JavaScript, для присоединения которого используется ключевое слово import.
К другой категории относятся модули Angular, используемые для описания приложений или групп взаимосвязанных
возможностей. У каждого приложения имеется корневой модуль, который предоставляет Angular информацию, необходимую для
запуска приложения.
При создании проекта с использованием angular-cli был создан файл с именем app.module.ts (стандартное имя файла
корневого модуля) в папке todo/src/app

```typescript
import {BrowserModule} from '@angular/platform-browser';
import {NgModule} from '@angular/core';
import {FormsModule} from '@angular/forms';
import {HttpModule} from '@angular/http';
import {AppComponent} from './app.component';

@NgModule({
    declarations: [AppComponent],
    imports: [BrowserModule, FormsModule, HttpModule],
    providers: [],
    bootstrap: [AppComponent]
})
export class AppModule {
}
```

Основная цель модуля Angular — передача конфигурационной информации в свойствах, определяемых декоратором @NgModule.
Свойство imports декоратора сообщает Angular, что приложение зависит от функций, необходимых для запуска приложений в
браузере, а свойства declarations и bootstrap сообщают Angular о компонентах приложения и о том, какой компонент должен
использоваться для запуска приложения (в этом простом примере компонент только один, поэтому он является значением обоих
свойств).

### Модуль загрузки

Приложениям Angular также необходим файл начальной загрузки с кодом, нужным для запуска приложения и загрузки модуля
Angular. Файл начальной загрузки имеет имя main.ts в папке todo/src со следующим кодом:

```typescript
import {enableProdMode} from '@angular/core';
import {platformBrowserDynamic} from '@angular/platform-browser-dynamic';
import {AppModule} from './app/app.module';
import {environment} from './environments/environment';

if (environment.production) {
    enableProdMode();
}
platformBrowserDynamic().bootstrapModule(AppModule);
```

Команды в файле начальной загрузки выбирают платформу, которая должна использоваться, и загружают корневой модуль,
который является точкой входа в приложение.

<blockquote style="color: rgba(255,162,162,0.8)">Вызов метода platformBrowserDynamic().bootstrapModule предназначен для браузерных приложений</blockquote>

### Принцип работы приложения Angular

> Браузер выполнил код из файла начальной загрузки; это привело к активизации среды Angular, которая, в свою очередь,
> обработала документ HTML и обнаружила элемент todo-app. Свойство selector, использованное для определения компонента,
> совпадает с элементом todo-app, вследствие чего Angular удаляет временный контент и зменяет его шаблоном компонента,
> автоматически загружаемым из файла app.component.html. В ходе разбора шаблона обнаруживается конструкция
> привязки {{ }};
> содержащееся в ней выражение вычисляется, вызывается метод getName и выводится результат

## Глава 2. Расширение функционала приложения Angular

### Добавление таблицы

Файл app.component.ts (Представление). <br>Добавление функции getTodoItems()

```typescript
import {Component} from "@angular/core";
import {Model} from "./model";

@Component({
    selector: "todo-app",
    templateUrl: "app/app.component.html"
})
export class AppComponent {
    model = new Model();

    getName() {
        return this.model.user;
    }

    getTodoItems() {
        return this.model.items;
    }
}
```

Файл app.component.html (Шаблон). <br>Добавление таблицы table. Добавление односторонней привязки данных. Добавление
шаблонных выражений ngFor, ngSwitch

```html
<h3 class="bg-primary p-a-1">{{getName()}}'s To Do List</h3>
<table class="table table-striped table-bordered">
    <thead>
    <tr>
        <th></th>
        <th>Description</th>
        <th>Done</th>
    </tr>
    </thead>
    <tbody>
    <tr *ngFor="let item of getTodoItems(); let i = index">
        <td>{{ i + 1 }}</td>
        <td>{{ item.action }}</td>
        <td [ngSwitch]="item.done">
            <span *ngSwitchCase="true">Yes</span>
            <span *ngSwitchDefault>No</span>
        </td>
    </tr>
    </tbody>
</table>
```

Файл app.component.model.ts (Модель данных)

```typescript
export class Model {
    user;
    items;

    constructor() {
        this.user = "Adam";
        this.items = [new TodoItem("Buy Flowers", false),
            new TodoItem("Get Shoes", false),
            new TodoItem("Collect Tickets", false),
            new TodoItem("Call Joe", false)]
    }
}

export class TodoItem {
    action;
    done;

    constructor(action, done) {
        this.action = action;
        this.done = done;
    }
}
```

### Добавление двусторонней привязи данных

Файл app.component.html (Шаблон). <br>Добавление ngModel

```html
<h3 class="bg-primary p-a-1">{{ getName() }}'s To Do List</h3>
<table class="table table-striped table-bordered">
    <thead>
    <tr>
        <th></th>
        <th>Description</th>
        <th>Done</th>
    </tr>
    </thead>
    <tbody>
    <tr *ngFor="let item of getTodoItems(); let i = index">
        <td>{{ i + 1 }}</td>
        <td>{{ item.action }}</td>
        <td><input type="checkbox" [(ngModel)]="item.done"/></td>
        <td [ngSwitch]="item.done">
            <span *ngSwitchCase="true">Yes</span>
            <span *ngSwitchDefault>No</span>
        </td>
    </tr>
    </tbody>
</table>
```

### Добавление функциональности приложения. Фильтрация задач

Файл app.component.ts. <br>Добавление <i>лямбда</i> функции filter()

```typescript
import {Component} from "@angular/core";
import {Model} from "./model";

@Component({
    selector: "todo-app",
    templateUrl: "app.component.html"
})
export class AppComponent {
    model = new Model();

    getName() {
        return this.model.user;
    }

    getTodoItems() {
        return this.model.items.filter(item => !item.done);
    }
}
```

### Добавление задач

Файл app.component.html. <br>Добавление переменной элемента input <i>"#"</i> и функцию обработки значения при нажатии (click) = (...)

```html
....
<div class="m-t-1 m-b-1">
    <input class="form-control" #todoText/>
    <button class="btn btn-primary m-t-1" (click)="addItem(todoText.value)">
        Add
    </button>
</div>
....
```

Файл app.component.ts. <br>Импорт данных и модели. Добавление функции addItem()

```typescript
...
import { Model, TodoItem } from "./model";
...
addItem(newItem) {
    if (newItem != "") {
        this.model.items.push(new TodoItem(newItem, false));
    }
}
...
```