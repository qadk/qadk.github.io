---
title: "API 輸出資料整理 - Adapter Pattern 學習筆記"
date: 2016-04-30
categories: ["PHP", "Design Pattern", "Clean Code"]
tags: ["Adapter Pattern", "API"]
---

最近專案需要以 API 的方式和前端進行溝通，但是覺得在每個 controller 的 method 中各自修改調整 API 所要的回傳值是個不太好的方案。在尋找更好的做法時發現了 [Fractal Transformer](http://fractal.thephpleague.com/transformers/) ，但因為回傳格式跟需求有些差異所以最後放棄使用這個套件，就參考了他的做法用簡略的方式和自己的理解滿足需求。

<!--more-->

### 適用情況

Client 需要特定的格式(例如製定好規格的資料欄位或是型別)，但是由原有/第三方的 Class 所提供的格式不能完全滿足需求時，就可以使用 Adapter 來轉接，讓兩邊程式碼都不需要為了這件事做額外的改動。

### 使用實例

參考 [Fractal Transformer](http://fractal.thephpleague.com/transformers/) 的做法，試著用自己理解和最簡單的方式實作一個能符合目前需求且基本的 Adapter 。

#### TransformerManager

```php
class TransformerManager
{
    public function collection(Collection $data, TransformerAbstract $transformer)
    {
        $result = [];
        foreach ($data as $index => $item) {
            $result[$index] = $this->transform($item, $transformer);
        }

        return $result;
    }

    public function item($data, TransformerAbstract $transformer)
    {
        return $this->transform($data, $transformer);
    }

    public function paginator(LengthAwarePaginator $paginator)
    {
        return [
            'total_count' => (int)$paginator->total(),
            'total_pages' => (int)$paginator->lastPage(),
            'current_page' => (int)$paginator->currentPage(),
            'limit' => (int)$paginator->perPage(),
        ];
    }

    protected function transform($item, TransformerAbstract $transformer)
    {
        $defaultIncludes = $transformer->getDefaultIncludes();

        if (empty($defaultIncludes) || !is_array($defaultIncludes)) {
            return $transformer->transform($item);
        }

        $result = $transformer->transform($item);

        foreach ($defaultIncludes as $include) {
            $method_name = 'include' . ucfirst($include);
            if (method_exists($transformer, "{$method_name}")) {
                $result = array_merge($result, [$include => $transformer->$method_name($item)]);
            }
        }

        return $result;
    }
}
```

#### TransformerAbstract

```php
abstract class TransformerAbstract
{
    protected $defaultIncludes = [];

    /**
     * @var TransformerManager
     */
    protected $manager;

    public function getManager()
    {
        if (empty($this->manager)) {
            $this->manager = new TransformerManager();
        }

        return $this->manager;
    }

    public function getDefaultIncludes()
    {
        return $this->defaultIncludes;
    }
}
```

#### ArticleTransformer

```php
class ArticleTransformer extends TransformerAbstract
{
    protected $defaultIncludes = ['author'];

    public function transform(Article $article)
    {
        return [
            'article_id' => $article->id,
            'subject' => $article->subject,
            'description' => $article->description,
            'created_at' => $article->created_at->diffForHumans(),
        ];
    }

    public function includeAuthor(Article $article)
    {
        return $this->getManager()->item($article->author, new UserTransformer);
    }
}
```

#### UserTransformer

```php
class UserTransformer extends TransformerAbstract
{
    public function transform(User $user)
    {
        return [
            'user_id' => $user->id,
            'name' => $user->name,
            'avatar' => $user->avatar,
        ];
    }
}
```

#### ArticlesController

```php
class ArticlesController extends Controller
{
    public function index(TransformerManager $transformerManager)
    {
        $articles = Article::paginator(20);

        return response()->json(
            [
                'type' => $tab,
                'data' => $transformerManager->collection($articles->getCollection(), new ArticlesListTransformer),
                'status' => 'success',
                'paginator' => $transformerManager->paginator($articles),
            ]
        );
    }

    public function show($id, TransformerManager $transformerManager)
    {
        $article = Article::find($id);

        return response()->json([
            'data' => [
                'article' => $transformerManager->item($article, new ArticleTransformer),
                'comments' => $this->transformerManager->collection($article->comments, new CommentTransformer),
            ],
            'status' => 'success'
        ]);
    }
}
```

#### 輸出結果

##### 分頁效果

```json
{
   "data": [
      {
         "article_id": 1,
         "subject": "Voluptates est rem vero odit earum.",
         "description": "Iste nihil animi et ipsa ipsa...",
         "created_at": "33 seconds ago",
         "author": {
            "user_id": 1,
            "name": "Sarah Prosacco DVM",
            "avatar": "http://lorempixel.com/640/480/?58341"
         }
      },
      ...
   ],
   "status": "success",
   "paginator": {
      "total_count": 9,
      "total_pages": 1,
      "current_page": 1,
      "limit": 20
   }
}
```

##### 單篇效果

```json
{
   "data": {
      "article": {
         "article_id": 9,
         "subject": "Rerum necessitatibus autem ab maiores incidunt.",
         "description": "Tenetur velit non dolorem quo dolorem optio aspernatur...",
         "created_at": "5 minutes ago",
         "author": {
            "user_id": 3,
            "name": "Gabrielle Green",
            "avatar": "http://lorempixel.com/640/480/?79444"
         }
      }
   },
   "status": "success"
}
```

### 心得

標題上面雖然寫 Adapter 學習筆記，但當時完全沒想到可以用 Adapter 來解，因為看過的範例大都用在第三方套件的串接上，直到實作完後查了點資料才知道這算是 Adapter ，所以寫這篇希望能幫上同樣有這種需求但不知道可以用這個 Pattern 來解的同伴們。

~~#跟在開源專案的尾巴偷師系列~~

### 參考資料

- [Adapter Pattern - Wiki](https://en.wikipedia.org/wiki/Adapter_pattern)
- [大話設計模式 - 在 NBA 我需要翻譯－轉接器模式 p.250](http://www.taaze.tw/sing.html?pid=11100126953)
- [OOP pattern to convert one data format to another](http://stackoverflow.com/questions/14209342/oop-pattern-to-convert-one-data-format-to-another)
- [Fractal - Output complex, flexible, AJAX/RESTful data structures.](https://github.com/thephpleague/fractal)
