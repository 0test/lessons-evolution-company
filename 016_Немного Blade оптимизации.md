# Оптимизация, наведение порядка

Я обнаружил, что имеются `main.blade.php`, `catalog.blade.php` и `catalog_group.blade.php`, в которых содержится абсолютно одинаковая разметка товара. 

Сделайте  файл `views\parts\product.blade.php` - копию разметки любого товара из лент.

```html
<div class="col mb-5">
    <div class="card h-100">
        <img class="card-img-top" src="{{ evo()->runSnippet('phpthumb',[ 
			'input' => $product['item_photo'],
			'options' => 'w=450,h=300,far=C'
			])
		 }}" />
        <div class="card-body p-4">
            <div class="text-center">
                <h5 class="fw-bolder">{{$product['pagetitle']}}</h5>

                <div class="price text-muted">{{ $product['item_price'] }} руб</div>
            </div>
        </div>
        <div class="card-footer p-4 pt-0 border-top-0 bg-transparent">
            <div class="text-center"><a class="stretched-link btn btn-outline-dark mt-auto"
                    href="{{ urlProcessor::makeUrl($product['id'] ) }}">Подробнее</a></div>
        </div>
    </div>
</div>
```

И теперь в файлах `main.blade.php`, `catalog.blade.php` и `catalog_group.blade.php` просто замените этот код на include.
Для примера `catalog.blade.php`:

```php
@extends('layouts.app')
@section('content')
<section class="container px-5">
    <div class="row">
        @include('parts/aside_catalog')
        <div class="col-md-9 ms-sm-auto col-lg-10 py-3">
            <section>
                <div class="container px-4 px-lg-5 mt-3">
                    <h1 class="pb-4">Каталог продукции</h1>
                    <div class="row py-2 gx-4 gx-lg-5 row-cols-1 row-cols-md-2 row-cols-xl-3 justify-content-center">
                        @foreach ($products as $product)
                            @include('parts.product')
                        @endforeach
                    </div>
                    <div class="row py-2 gx-5">
                        {!! $modx->getPlaceholder('pages') !!}
                    </div>
                </div>
            </section>
        </div>
    </div>
</section>
@endsection
```

Таким образом разметка товара (в лентах) на всём сайте теперь лежит в одном-единственном файле. Это удобно.

Кстати и блок "Похожие товары" тоже можно оптимизировать, ведь код товара везде одинаков:
```html
<section class="py-5 bg-light">
    <div class="container px-4 px-lg-5 mt-5">
        <h2 class="fw-bolder mb-4">Похожие товары</h2>
        <div class="row gx-4 gx-lg-5 row-cols-2 row-cols-md-3 row-cols-xl-4 justify-content-center">
            @foreach ($related_products as $product)
                @include('parts.product')
            @endforeach
        </div>
    </div>
</section>
```

Попутно я обнаружил похожую петрушку и с новостями. А именно: блок "Ещё новости", сама лента новостей и "Последние новости" на главной содержат абсолютно одинаковый код для каждой новости.

Я также вынесу эти сущности в отдельный файл `parts/news.blade.php`. Сделайте это по аналогии с товарами.

**Что может пойти не так?**

В разных файлах мы делали разные переменные для цикла. А вот файл `parts/news.blade.php` работает только с переменной `news_one`. Если получите ошибку, обратите внимание на цикл и на то, какие переменные он прокинет в наш `include('parts.news')`
```php
<section class="py-5">
    <div class="container px-5">
        <h2 class="fw-bolder fs-5 mb-4">Ещё новости</h2>
        <div class="row gx-5">
            @foreach ($other_news as $news_one)
           	 @include('parts.news')
            @endforeach
        </div>
    </div>
</section>
```

## Итого:
- На сайте стало чуть меньше одинаковой "лапши" из верстки

---

Что касается контента - практически вся информация на сайте выводится автоматически, остались мелочи.
Давайте поработаем с блоками [на главной странице и на странице контактов](/017_Главная%20страница,%20страница%20Контакты.md).
