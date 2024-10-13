# Внешний вид

- [Основы](#basics)
- [Группы](#groups)
- [Разделитель](#divider)
- [Иконка](#icon)
- [Метка](#badge)
- [Перевод](#translation)
- [Открытие в новой вкладке](#target-blank)
- [Условие отображения](#condition)
- [Активный пункт](#active)
- [Атрибуты](#attributes)
- [Изменение кнопки](#change-button)
- [Изменение шаблона](#custom-view)

---

<a name="basics"></a>
## Основы

**Menu** является основой для навигации по админ-панели, поэтому мы постарались создать гибкую систему, которая позволит вам сделать полную кастомизацию меню для разных старниц и пользователей.

Настройка навигационного меню осуществляется в классе который расширяет `MoonShine\Laravel\Layouts\AppLayout` через метод `menu()`. 

В процессе установки админ-панели, в зависимости от выбранных вами конфигураций, будет создан класс **App\MoonShine\Layouts\MoonShineLayout**, который уже содержит метод `menu()`.

В дальнейшем, если вам потребуется, вы сможете создавать другие ***Layout*** для определенных страниц.

Для того чтобы добавить пункт меню, необходимо воспользоваться классом **MoonShine\Menu\MenuItem**
и его статическим методом `make()`.

```php
MenuItem::make(Closure|string $label, Closure|MenuFillerContract|string $filler, string $icon = null, Closure|bool $blank = false)
```

- `$label` - название пункта меню,
- `$filler` - элемент для формирования url,
- `$icon` - иконка для пункта меню,
- `$blank` - открыть в новой вкладке.

> [!TIP]
> В качестве второго параметра можно передать [ModelResource](), [Page]() или [Resource]().

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\Laravel\Resources\MoonShineUserResource;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuItem::make('Admins', new MoonShineUserResource()),
            MenuItem::make('Home', fn() => route('home')),
            MenuItem::make('Docs', 'https://moonshine-laravel.com/docs'),
            MenuItem::make('Laravel Docs', 'https://laravel.com/docs', blank: true)
        ];
    }
}
```

> [!TIP]
> Если меню создается для [ModelResource]() или [Resource](), для элемента меню будет использоваться первая страница объявленная в методе <code>pages()</code>.

<a name="groups"></a>
## Группы

Пункты меню можно объединять в группы. Для этого используется класс `MoonShine\MenuManager\MenuGroup`
со статическим методом `make()`.

```php
MenuGroup::make(Closure|string $label, iterable $items, string|null $icon = null)
```

- `$label` - название группы,
- `$items` - массив компонентов меню,
- `$icon` - иконка для группы.

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\Laravel\Resources\MoonShineUserResource;
use MoonShine\Laravel\Resources\MoonShineUserRoleResource;
use MoonShine\MenuManager\MenuGroup;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuGroup::make('System', [
                MenuItem::make('Admins', new MoonShineUserResource()),
                MenuItem::make('Roles', new MoonShineUserRoleResource()),
            ])
        ];
    }
}
```

Так же группе можно добавить элементы через метод `setItems()`.

```php
setItems(iterable $items)
```

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\Laravel\Resources\MoonShineUserResource;
use MoonShine\Laravel\Resources\MoonShineUserRoleResource;
use MoonShine\MenuManager\MenuGroup;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuGroup::make('System')->setItems([
                MenuItem::make('Admins', new MoonShineUserResource()),
                MenuItem::make('Roles', new MoonShineUserRoleResource()),
            ])
        ];
    }
}
```

> [!TIP]
> Для создания многоуровневого меню, группы можно делать вложенными.

<a name="divider"></a>
## Разделитель

Пункты меню можно визуально разделить с помощью `MenuDivider`.

```php
/**
 * @param  (Closure(MenuElementContract $context): string)|string  $label
 */
MenuDivider::make(Closure|string $label = '')
```

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\Laravel\Resources\MoonShineUserResource;
use MoonShine\Laravel\Resources\MoonShineUserRoleResource;
use MoonShine\MenuManager\MenuDivider;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuItem::make('Admins', new MoonShineUserResource()),
            MenuDivider::make(),
            MenuItem::make('Roles', new MoonShineUserRoleResource())
        ];
    }
}
```

<a name="icon"></a>
## Иконка

У пункта меню и у группы можно задать иконку. Это можно реализовать несколькими методами.

### Через параметр
Иконку можно задать, передав третьим параметром название в статическом методе `make()`.

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\Laravel\Resources\MoonShineUserResource;
use MoonShine\Laravel\Resources\MoonShineUserRoleResource;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuItem::make('Admins', new MoonShineUserResource(), 'users'),
            MenuItem::make('Roles', new MoonShineUserRoleResource(), 'hashtag')
        ];
    }
}
```

### Через метод

Воспользоваться методом `icon()`.

```php
icon(string $icon, bool $custom = false, ?string $path = null)
```

- `$icon` - название иконки или html (если используется кастомный режим),
- `$custom` - кастомный режим,
- `$path` - путь до директории где лежат **blade** шаблоны иконок.

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\Laravel\Resources\MoonShineUserResource;
use MoonShine\Laravel\Resources\MoonShineUserRoleResource;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuGroup::make('System', [
                MenuItem::make('Admins', new MoonShineUserResource())
                    ->icon('users'), 
                MenuItem::make('Roles', new MoonShineUserRoleResource())
                    ->icon(svg('path-to-icon-pack')->toHtml(), custom: true), 
            ])
                ->icon('cog', path: 'icons') 
        ];
    }
}
```

### Через атрибут

У пункта меню отобразится иконка, если у класса **ModelResource**, **Page** или **Resource** задан атрибут `Icon` и иконка не переопределена другими способами.

```php
namespace MoonShine\Resources;
 
#[Icon('users')] 
class MoonShineUserResource extends ModelResource
{
 
    //...
 
}
```

> [!TIP]
> За более подробной информацией обратитесь к разделу [Иконки](/docs/{{version}}/appearance/icons).

<a name="badge"></a>
## Метка

Также есть возможность добавить значок к пункту меню.

### Через элемент меню

Для добавления значка к пункту меню используется метод `badge()`, который в качестве параметра принимает замыкание.

```php
/**
 * @param  Closure(MenuElementContract $context): string|int|float|null  $value
 */
badge(Closure|string|int|float|null $value)
```

```php
namespace App\MoonShine\Layouts;

use App\MoonShine\Resources\CommentResource;
use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuItem::make('Comments', new CommentResource())
                ->badge(fn() => Comment::count()) 
        ];
    }
}
```

<a name="translation"></a>
## Перевод

Для перевода пунктов меню необходимо в качестве названия передать ключ перевода и добавить метод `translatable()`.

```php
translatable(string $key = '')
```

```php
namespace App\MoonShine\Layouts;

use App\MoonShine\Resources\CommentResource;
use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuItem::make('menu.Comments', new CommentResource())
                ->translatable() 
            // or
            MenuItem::make('Comments', new CommentResource())
                ->translatable('menu')  
        ];
    }
}
``` 

```php
// lang/ru/menu.php
 
return [
    'Comments' => 'Комментарии',
];
```

Для перевода меток меню можно воспользоваться средствами перевода Laravel.

```php
namespace App\MoonShine\Layouts;

use App\MoonShine\Resources\CommentResource;
use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuItem::make('Comments', new CommentResource())
                ->badge(fn() => __('menu.badge.new')) 
        ];
    }
}
```

<a name="target-blank"></a>
## Открытие в новой вкладке

У пункта меню можно указать флаг, указывающий, открывать ссылку в новой вкладке или нет. Это можно реализовать несколькими способами.

### Через параметр

Флаг можно задать, передав четвёртым параметром `true/false` или замыкание в статическом методе `make()`.

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuItem::make('MoonShine Docs', 'https://moonshine-laravel.com/docs', 'heroicons.arrow-up', true), 
            MenuItem::make('Laravel Docs', 'https://laravel.com/docs', blank: fn() => true), 
        ];
    }
}
```

### Через метод

Воспользоваться методом `blank()`.

```php
/**
 * @param  (Closure(MenuElementContract $context): bool)|bool  $blankCondition
 */
blank(Closure|bool $blankCondition = true)
```

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuItem::make('MoonShine Docs', 'https://moonshine-laravel.com/docs', 'heroicons.arrow-up', true), 
            MenuItem::make('Laravel Docs', 'https://laravel.com/docs', blank: fn() => true), 
        ];
    }
}
```

<a name="condition"></a>
## Условие отображения

Отображать элементы меню можно по условию, воспользовавшись методом `canSee()`.

```php
/**
 * @param  Closure(MenuElementContract $context): bool  $callback
 */
canSee(Closure $callback)
```

```php
namespace App\Providers;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\Laravel\Resources\MoonShineUserResource;
use MoonShine\Laravel\Resources\MoonShineUserRoleResource;
use MoonShine\MenuManager\MenuDivider;
use MoonShine\MenuManager\MenuGroup;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuGroup::make('System', [
                MenuItem::make('Admins', new MoonShineUserResource()),
                MenuDivider::make()
                    ->canSee(fn()=> true),
                MenuItem::make('Roles', new MoonShineUserRoleResource())
                    ->canSee(fn()=> false)
            ])
                ->canSee(static fn(): bool => request()->user('moonshine')?->id === 1)
        ];
    }
}
```

<a name="active"></a>
## Активный пункт

Пункт меню становится активным если он соответствует ***url***, но метод `forceActive()` позволяет принудительно сделать пункт активным.

```php
/**
 * @param  Closure(string $path, string $host, MenuElementContract $context): bool  $when
 */
whenActive(Closure $when)
```

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuItem::make('Label', '/endpoint')
                ->whenActive(fn() => request()->fullUrlIs('*admin/endpoint/*')),  
        ];
    }
}
```

<a name="attributes"></a>
## Атрибуты

Группам и элементам меню, как и другим компонентам, можно назначить кастомные атрибуты.

> [!TIP]
> За более подробной информацией обратитесь к разделу [Атрибуты компонентов](/docs/{{version}}/components/attributes)

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\Laravel\Resources\MoonShineUserResource;
use MoonShine\Laravel\Resources\MoonShineUserRoleResource;
use MoonShine\MenuManager\MenuGroup;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuGroup::make('System')->setItems([
                MenuItem::make('Admins', new MoonShineUserResource()),
                MenuItem::make('Roles', new MoonShineUserRoleResource())
                    ->customAttributes(['class' => 'group-li-custom-class'])
            ])
                ->setAttribute('data-id', '123')
                ->class('group-li-custom-class')
        ];
    }
}
```

<a name="change-button"></a>
## Изменение кнопки

Пункт меню является [ActionButton](#) и изменить его атрибуты можно воспользовавшись методом `changeButton`.

```php
/**
 * @param  Closure(ActionButtonContract $button): ActionButtonContract  $callback
 */
changeButton(Closure $callback)
```

```php
namespace App\MoonShine\Layouts;

use MoonShine\UI\Components\ActionButton
use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuItem::make('Label', '/endpoint')
                ->changeButton(static fn(ActionButton $button) => $button->class('new-item')),  
        ];
    }
}
```

> [!WARNING]
> Некоторые параметры **ActionButton**, такие как `url`, `badge`, `icon` и другие, системно переопределяются. Для их изменения используйте соответствующие методы.

<a name="custom-view"></a>
## Изменение шаблона

Когда необходимо изменить **view** с помощью *fluent interface* можно воспользоваться методом `customView()`.

```php
customView(string $path)
```

- `$path` - путь до **blade** шаблона.

```php
namespace App\MoonShine\Layouts;

use MoonShine\Laravel\Layouts\AppLayout;
use MoonShine\MenuManager\MenuGroup;
use MoonShine\MenuManager\MenuItem;

final class MoonShineLayout extends AppLayout
{

    //...
    
    protected function menu(): array
    {
        return [
            MenuGroup::make('Group', [
                MenuItem::make('Label', '/endpoint')
                    ->customView('admin.custom-menu-item'), 
            ])
                ->customView('admin.custom-menu-group'),   
        ];
    }
}
```