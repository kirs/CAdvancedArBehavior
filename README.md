The CAdvancedArBehavior extension adds up some functionality to the default possibilites of yii´s ActiveRecord implementation. At the moment it is able to automatically save MANY_MANY relation objects when save()-ing an Object.

## Changelog 

* Version 0.4 added 14.08.2011 by kirs

Relations will be destroyed after model deletion, duplications of many2many relations won't be saved.

* Version 0.3 added 25.05.2011 by thyseus

added `$ignoreRelations` to ignore specified relations. The Behavior will take all found many2many relations by default. Specify exceptions in this array.
fixes all the bugs and glitches found in the discussion

# Installation 

To use this extension, just copy this file to your extensions/ directory, add `'import' => 'application.extensions.CAdvancedArBehavior', [...]` to your `config/main.php` and add this behavior to each model you would like to inherit the new possibilities.

    public function behaviors(){
      return array( 'CAdvancedArBehavior' => array(
        'class' => 'application.extensions.CAdvancedArBehavior'));
    }

# Possibilities: 

### Better support of MANY_TO_MANY relations:

When we have defined a MANY_MANY relation in our relations() function, we are now able to add up instances of the foreign Model on the fly while saving our Model to the Database. Let´s assume the following Relation:

Post has: `'categories'=>array(self::MANY_MANY, 'Category', 'tbl_post_category(post_id, category_id)')`

Category has: `'posts'=>array(self::MANY_MANY, 'Post', 'tbl_post_category(category_id, post_id)')`

Now we can use the attribute 'categories' of our Post model to add up new rows to our MANY_MANY connection Table:

    $post = new Post();
    $post->categories = Category::model()->findAll();
    $post->save();
    
This will save our new Post in the table Post, and in addition to this it updates our N:M-Table with every Category available in the Database.

We can further limit the Objects given to the attribute, and can also go the other Way around:

    $category = new Category();
    $category->posts = array(5, 6, 7, 10);
    $category->save();
    
We can pass Object instances like in the first example, or a list of integers that representates the Primary key of the Foreign Table, so that the Posts with the id 5, 6, 7 and 10 get´s added up to our new Category.

Queries will be performed here, one for the Category-Model and four for the N:M-Table tbl_post_category. Note that this behavior could be tuned further in the future, so only one query get´s executed for the MANY_MANY Table.

We can also pass a single object or an single integer:

    $category = new Category();
    $category->posts = Post::model()->findByPk(12);
    $category->posts = 12;
    $category->save();

# Credits

Originaly written by [thesus](http://www.yiiframework.com/user/1134/).

In August 2011 extension was improved by Kirs for [Navigator-edu](http://navigator-edu.ru/).