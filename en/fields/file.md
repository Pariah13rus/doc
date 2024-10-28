# File

  - [Basics](#basics)  
  - [Disk](#disk)  
  - [Directory](#dir)  
  - [Valid extensions](#allowed-extensions)  
  - [Multiload](#multiple)  
  - [Deleting files](#removable)  
  - [Ban on downloading](#download)
  - [Original file name](#filename)
  - [Custom file name](#customname)
  - [Element names](#name)
  - [Item attributes](#item-attributes)
  - [Helper methods](#helper-methods)

---

<a name="basics"></a>
## Basics

> [!TIP]
> Before use, you must make sure that the **storage** directory a symbolic link has been set.  
> `php artisan storage:link`

The _File_ field is used to upload files and includes all the basic methods.

```php
use MoonShine\Fields\File;

//...

public function fields(): array
{
    return [
        File::make('File')
    ];
}

//...
```

![File Dark](https://raw.githubusercontent.com/moonshine-software/doc/2.x/resources/screenshots/file_dark.png)

> [!NOTE]
> To generate the file URL correctly, you must define the `APP_URL` environment variable in this way, so that it matches your application's URL.

<a name="disk"></a>
## Disk

The `disk()` method allows you to change the _filesystems disk_.

```php
disk(string $disk)
```

```php
use MoonShine\Fields\File;
 
//...
 
public function fields(): array
{
    return [
        File::make('File')
            ->disk('public') 
    ];
}
 
//...
```

> [!NOTE]
> The default is _disk_ `public`. You can change it in the file  [configurations](https://moonshine-laravel.com/docs/resource/getting-started/configuration).

> [!NOTE] 
>When using the `local` driver, the `url` value returned is not a URL. For this reason, we >recommend that you always store your files with names that will create valid URLs.

<a name="dir"></a>
## Directory

By default, files will be saved in the root directory of the disk.
The `dir()` method allows you to specify the directory to save files relative to the root directory.

```php
dir(string $dir)
```

```php
use MoonShine\Fields\File;
 
//...
 
public function fields(): array
{
    return [
        File::make('File')
            ->dir('docs') 
    ];
}
 
//...
```

<a name="allowed-extensions"></a>
## Valid extensions

Using the `allowedExtensions()` method you can specify which files will be available for download.

```php
allowedExtensions(array $allowedExtensions)
```

```php
use MoonShine\Fields\File;
 
//...
 
public function fields(): array
{
    return [
        File::make('File')
            ->allowedExtensions(['pdf', 'doc', 'txt']) 
    ];
}
 
//...
```

<a name="multiple"></a>
## Multiload

To load multiple files, use the `multiple()` method.

```php
multiple(Closure|bool|null $condition = null)
```

```php
use MoonShine\Fields\File;
 
//...
 
public function fields(): array
{
    return [
        //...
        File::make('File')
            ->multiple(), 
        //...
    ];
}
 
//...
```

> [!ERROR]
> The field in the database must be of _text_ or _json_ type.
> You also need to add a cast for the eloquent model - *json*, or *array*, or *collection*.

<a name="removable"></a>
## Deleting files
To be able to delete files, you must use the `removable()` method.

```php
removable(
    Closure|bool|null $condition = null,
    array $attributes = []
)
```

-`$condition` - condition for executing the method
-`$attributes` - additional button attributes

```php
use MoonShine\Fields\File;

//...

public function fields(): array
{
    return [
        File::make('File')
            ->removable()
    ];
}

//...
```

#### Attributes

```php
use MoonShine\Fields\File;

//...

public function fields(): array
{
    return [
        File::make('File')
            ->removable(
                attributes: ['@click.prevent' => '$event.target.closest(`.x-removeable`).remove()']
            )
    ];
}

//...
```

#### disableDeleteFiles()

The `disableDeleteFiles()` method will allow you to delete only a record in the database, but do not delete the file itself.

```php
disableDeleteFiles()
```
```php
use MoonShine\Fields\File;

//...

public function fields(): array
{
    return [
        File::make('File')
            ->removable()
            ->disableDeleteFiles()
    ];
}

//...
```

#### enableDeleteDir()

The `enableDeleteDir()` method deletes the directory specified in the `dir()` method if it is empty.

```php
enableDeleteDir()
```

```php
use MoonShine\Fields\File;

//...

public function fields(): array
{
    return [
        File::make('File')
            ->dir('docs')
            ->removable()
            ->enableDeleteDir()
    ];
}

//...
```

<a name="download"></a>
## Ban on downloading

The `disableDownload()` method allows you to exclude the possibility of downloading a file.

```php
disableDownload(Closure|bool|null $condition = null)
```

```php
use MoonShine\Fields\File;

//...

public function fields(): array
{
    return [
        File::make('File', 'file')
            ->disableDownload()
    ];
}

//...
```

<a name="filename"></a>
## Original file name

When loading, a file name is generated by default.The `keepOriginalFileName()` method allows you to save the file with the original name.

```php
keepOriginalFileName()
```

```php
use MoonShine\Fields\File;

//...

public function fields(): array
{
    return [
        File::make('File')
            ->keepOriginalFileName()
    ];
}

//...
```

<a name="customname"></a>
## Custom file name

The `customName()` method allows you to save a file with a custom name.

```php
customName(Closure $name)
```

```php
use MoonShine\Fields\File;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Str;

//...

public function fields(): array
{
    return [
        File::make('File', 'file')
            ->customName(fn(UploadedFile $file, Field $field) =>  Str::random(10) . '.' . $file->extension())
    ];
}

//...
```

<a name="names"></a>
## Element names

The `names()` method allows you to change the display name without changing the file name.

```php 
names(Closure $closure)
```

- `$closure` - the closure takes the file name and file index.

```php
use MoonShine\Fields\File;

//...

public function fields(): array
{
    return [
        File::make('File', 'file')
            ->names(fn(string $filename, int $index = 0) => 'File ' . $index + 1)
    ];
}

//...
```

<a name="item-attributes"></a>
## Item attributes

The `itemAttributes()` method allows you to add additional attributes to elements.

```php
itemAttributes(Closure $closure)
```

- `$closure` - the closure takes the file name and file index.

```php
use MoonShine\Fields\File;

//...

public function fields(): array
{
    return [
        File::make('File', 'file')
            ->itemAttributes(fn(string $filename, int $index = 0) => [
                'style' => 'width: 250px; height: 250px;'
            ])
    ];
}

//...
```
<a name="helper-methods"></a>
## Helper methods

#### getRemainingValues()

The `getRemainingValues()` method allows you to get the values that remained in the form, taking into account the deletion.

```php
getRemainingValues()
```

#### removeExcludedFiles()

The `removeExcludedFiles()` method allows you to physically remove files during the process.

```php
removeExcludedFiles()
```

> [!TIP]
> Recipe: [saving images](https://moonshine-laravel.com/docs/resource/recipes/recipes#images-in-linked-table) in the linked table.