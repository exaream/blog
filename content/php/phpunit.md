---
date: 2020-01-01
lastmod: ["lastmod"]
title: PHPUnit
tags: ["PHPUnit","Laravel","Test"]
---

# PHPUnit

## 事前準備

### インストール
```composer.json```
```yaml {hl_lines=["2-5"]}
"require-dev": {
    "fzaninotto/faker": "*",
    "mikey179/vfsstream": "*",
    "mockery/mockery": "*",
    "phpunit/phpunit": "*"
},
```

```bash
$ composer install
```

### バージョンの確認
```bash
$ ./vendor/bin/phpunit --version
```

## 基本

```php
<?php

use Aaa\ClassA;
use Bbb\ClassB;
use Eee\ExceptionA;
use Mockery;
use PHPUnit\Framework\TestCase;

/**
 * @coversDefaultClass Aaa\ClassA
 */
class ClassATest extends TestCase
{

    // Omitted
    /**
     * This method is called before class.　TestCaseクラス実行前の処理。
     *
     * @return void
     */
    public static function setUpBeforeClass(): void
    {
        parent::setUpBeforeClass();
    
        // static 変数を使用（static メソッドのため）
        self::$hoge = 'some value';
    }
    
    
    /**
     * This method is called before each test.　各テストのメソッドが実行される前の処理。
     *
     * @return void
     */
    protected function setUp(): void
    {
        parent::setUp();

        // インスタンス変数
        $this->classA = new ClassA();
    }
    
        
    /**
     * This method is called after each test. 各テストのメソッドが実行された後の処理。
     *
     * @return void
     */
    protected function tearDown(): void
    {
        parent::tearDown();
    }
    
    
    /**
     * Test ClassA::method1
     *
     * @covers ::method1  カバレッジを算出する際に使用。テスト対象のクラス名（名前空間を含む）とメソッド名を記載。
     * @return void
     */
    public function testMethod1ExpectedValue(): void // メソッド名の命名規則：lowerCamelCase  test[TargetFunctionName][Description|ExpectedValue]
    {
        $classA = new ClassA();
        $actualValue = $classA->method1();
        $this->assertEquals($expectedValue, $actualValue); // 引数は期待値、実際の値の順番
    }
}
```

## アノテーション

### デフォルトのテスト対象クラスをテスト・クラスのアノテーションで指定
メソッドのアノテーションでクラス名の記述を省略しクラス名のタイポを未然に防止
```@coversDefaultClass```
https://phpunit.readthedocs.io/en/latest/annotations.html#coversdefaultclass
```php {hl_lines=[5,10]}
<?php
use PHPUnit\Framework\TestCase;

/**
 * @coversDefaultClass \Foo\CoveredClass
 */
class CoversDefaultClassTest extends TestCase
{
    /**
     * @covers ::publicMethod
     */
    public function testSomething()
    {
        $o = new Foo\CoveredClass;
        $o->publicMethod();
    }
}
```

### 未実装のテストをスキップする

```php {hl_lines=[13]}
use Aaa\ClassA;
 
// Omitted
 
/**
 * Skip unimplemented tests
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $this->markTestIncomplete('This test has not been implemented yet.');
}
```

### アサーションがないテストをリスキーと見なさない

```php {hl_lines=[5]}
/**
 * Do not consider a test without an assertion as risky
 *
 * @covers Aaa\ClassA::method1ClassA
 * @doesNotPerformAssertions
 * @return void
 */
public function testSampleMethod(): void
{
    // Omitted
}
```

### PHP Code Sniffer で警告が発生するのを 以下のアノテーションで回避

コールバック関数で使用する引数が未使用扱いとなり PHP Code Sniffer で警告が発生するのを 以下のアノテーションで回避
```php {hl_lines=[4,8]}
protected function getSample() : callable
{
    // PHP Code Sniffer で警告が発生するのを 以下のアノテーションで回避
    // @codingStandardsIgnoreStart
    return function ($sample) {
        // Omitted
    }
    // @codingStandardsIgnoreEnd
}
```


## 例外

### 例外が発生することをテスト

#### PHPUnit 8+

```php {hl_lines=[14]}
use Aaa\ClassA;
use Eee\ExceptionA
 
// Omitted
 
/**
 * Test that ExceptionA occurs
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $this->expectException(ExceptionA::class);　// 　クラス名（名前空間を含む）の文字列を使用する場合は クラス名::class を使用
    $classA = new ClassA();
    $classA->method1ClassA();
}
```

#### PHPUnit 7

```php {hl_lines=[10]}
use Aaa\ClassA;
use Eee\ExceptionA
 
// Omitted
 
/**
 * Test that Exception occurs
 *
 * @covers Aaa\ClassA::method1ClassA
 * @expectedException Eee\ExceptionA
 * @return void
 */
public function testSampleMethod(): void
{
    $classA = new ClassA();
    $classA->method1ClassA();
}
```

### 例外が発生しないことをテスト

```php {hl_lines=["15-20"]}
use Aaa\ClassA;
use Eee\ExceptionA
 
// Omitted
 
/**
 * Test that no ExceptionA occurs
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $classA = new ClassA();
    try {
        $classA->method1ClassA();
    } catch (ExceptionA $e) {
        $this->fail();
    }
    $this->assertTrue(true);
}
```

## public メソッド,プロパティ

### publicメソッドのモックを作ってテスト

```php {hl_lines=["16-19"]}
use Aaa\ClassA;
use Bbb\ClassB;
use Eee\ExceptionA
use Mockery;
 
// Omitted
 
/**
 * Test using a mock of public method
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
   $mockClassB = Mockery::mock(ClassB::class);
   // 引数は with() で指定、返り値は andReturn() で指定
   $mockClassB->shouldReceive('method1ClassB')->with('argument value 1', 'argument value 2')->andReturn('some value');
   $classA = new ClassA($mockClassB);
   $classA->method1ClassA();
}
```

### publicプロパティのモックを作ってテスト

```php {hl_lines=["15-17"]}
use Aaa\ClassA;
use Bbb\ClassB;
use Mockery;
 
// Omitted
 
/**
 * Test using a mock of public property
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $mockClassB = Mockery::mock(ClassB::class);
    $mockClassB->property1ClassB = 'some value';
    $classA = new ClassA($mockClassB);
    $classA->method1ClassA();
}
```

### public staticメソッドのモックを作ってテスト

```@runInSeparateProcess``` そのテストを個別の PHP プロセスで実行するように指示する

```php {hl_lines=["17-18"]}
use Aaa\ClassA;
use Bbb\ClassB;
use Mockery;
 
// Omitted
 
/**
 * Test using a mock of public static method
 *
 * @covers Aaa\ClassA::method1ClassA
 * @runInSeparateProcess
 * @preserveGlobalState disabled
 * @return void
 */
public function testSampleMethod(): void
{
    $mockClassB = Mockery::mock('alias:' . ClassB::class);
    $mockClassB->shouldReceive('method1ClassB')->andReturn('some value');
    $classA = new ClassA();
    $classA->method1ClassA();
}
```

### publicメソッドのモックを作ってテスト（クラス名がハードコーディングされている場合）

```php {hl_lines=["17-18"]}
use Aaa\ClassA;
use Bbb\ClassB;
use Mockery;
 
// Omitted
 
/**
 * Test using a mock of public method if a class name is hard coded
 *
 * @covers Aaa\ClassA::method1ClassA
 * @runInSeparateProcess
 * @preserveGlobalState disabled
 * @return void
 */
public function testSampleMethod(): void
{
    $mockClassB = Mockery::mock('overload:' . ClassB::class);
    $mockClassB->shouldReceive('method1ClassB')->andReturn('some value');
    $classA = new ClassA();
    $classA->method1ClassA();
}
```

### 自クラスのpublicメソッドのモックを作ってテスト

```php {hl_lines=["14-15"]}
use Aaa\ClassA;
use Mockery;
 
// Omitted
 
/**
 * Test using a mock of public method of the class itself
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $partialMockClassA = Mockery::mock(ClassA::class)->makePartial();
    $partialMockClassA->shouldReceive('method2ClassA')->andReturn('some value');
    $partialMockClassA->method1ClassA();
}
```

### 依存クラスのコンストラクタ呼び出しをモックしてテスト

```php {hl_lines=[10,"18-21"]}
namespace Aaa;
use Bbb\ClassB;
 
// Omitted
 
class ClassA
{
    public function method1ClassA()
    {
        $classB = $this->newClassB();
        // Omitted
    }
 
    /**
     * Wrap the instance generation process because the mock of the constructor cannot be created
     * コンストラクタのモックは作れないのでインスタンス生成処理をラップする
     */
    public function newClassB(): ClassB
    {
        return new ClassB();
    }
}
```

```php {hl_lines=["15-17"]}
use Aaa\ClassA;
use Bbb\ClassB;
use Mockery;

// Omitted

/**
 * Mock constructor call of a dependent class and test
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $mockClassB = Mockery::mock(ClassB::class);
    $partialMockClassA = Mockery::mock(ClassA::class)->makePartial();
    $partialMockClassA->shouldReceive('newClassB')->andReturn($mockClassB);
    $partialMockClassA->method1ClassA();
}
```

### チェインしたpublicメソッドのモックを作ってテスト
チェーンしたメソッドが引数をとる場合は1つずつ Mockery でモック化する必要あり
```php {hl_lines=["15-17"]}
use Aaa\ClassA;
use Bbb\ClassB;
use Mockery;

// Omitted

/**
 * Test using a mock of chained public method
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $mockClassB = Mockery::mock(ClassB::class);
    $mockClassB->shouldReceive('method1ClassB->method2ClassB')->andReturn('some value');
    $classA = new ClassA($mockClassB);
    $classA->method1ClassA();
}
```

### [Laravel] ファサードのpublicメソッドのモックを作ってテスト

```php {hl_lines=[15]}
use Aaa\ClassA;
use Fff\FacadeA;
use Mockery;
 
// Omitted
 
/**
 * Test using a mock of a public method of Laravel Facade
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    FacadeA::shouldReceive('method1FacadeA')->andReturn('some value');
    $classA = new ClassA();
    $classA->method1ClassA();
}
```

### [Laravel] Eloquentのpublicメソッドのモックを作ってテスト

```php {hl_lines=["15-17"]}
use Aaa\ClassA;
use Eee\EloquentA;
use Mockery;

// Omitted

/**
 * Test using a mock of a public method of Laravel Eloquent
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $mockEloquentA = Mockery::mock(EloquentA::class);
    $mockEloquentA->shouldReceive('method1EloquentA')->andReturn('some value');
    $classA = new ClassA($mockEloquentA);
    $classA->method1ClassA();
}
```

### [Laravel] Eloquentのpublicプロパティのモックを作ってテスト

```php {hl_lines=["15-17"]}
use Aaa\ClassA;
use Eee\EloquentA;
use Mockery;
 
// Omitted
 
/**
 * Test using a mock of a public property of Laravel Eloquent
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $mockEloquentA = Mockery::mock(EloquentA::class)->makePartial();
    $mockEloquentA->property1EloquentA = 'some value';
    $classA = new ClassA($mockEloquentA);
    $classA->method1ClassA();
}
```

## private/protected

### private/protectedメソッドをテスト

```php {hl_lines=["15-17"]}
use Aaa\ClassA;
use ReflectionMethod;
 
// Omitted
 
/**
 * Test private/protected methods
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $classA = new ClassA();
    $reflectionMethod1ClassA = new ReflectionMethod($classA, 'method1ClassA');
    $reflectionMethod1ClassA->setAccessible(true);
    $reflectionMethod1ClassA->invoke($classA);
}
```

### private/protectedプロパティの値を書き換えてテスト

```php {hl_lines=["15-18"]}
use Aaa\ClassA;
use ReflectionClass;
 
// Omitted
 
/**
 * Rewrite private/protected property value and test
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $classA = new ClassA();
    $reflectionClassA = new ReflectionClass($classA);
    $reflectionProperty1ClassA = $reflectionClassA->getProperty('property1ClassA');
    $reflectionProperty1ClassA->setAccessible(true);
    $reflectionProperty1ClassA->setValue($classA, 'some value');
    $classA->method1ClassA();
}
```

### private/protectedプロパティの値をテスト

```php {hl_lines=["16-19"]}
use Aaa\ClassA;
use ReflectionClass;
 
// Omitted
 
/**
 * Test private/protected property value
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $classA = new ClassA();
    $classA->method1ClassA();
    $reflectionClassA = new ReflectionClass($classA);
    $reflectionProperty1ClassA = $reflectionClassA->getProperty('property1ClassA');
    $reflectionProperty1ClassA->setAccessible(true);
    $valueReflectionProperty1ClassA = $reflectionProperty1ClassA->getValue($classA);
    $this->assertEquals('some value', $valueReflectionProperty1ClassA);
}
```

## ファイル

### 仮想ファイルを使ってファイルの読み書きをテスト

```php {hl_lines=["14-16"]}
use Aaa\ClassA;
use org\bovigo\vfs\vfsStream;
 
// Omitted
 
/**
 * Test reading and writing a file using a virtual file
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $root = vfsStream::setup('root');
    vfsStream::newFile('file')->at($root)->setContent('some contents');
    $path = vfsStream::url('root/file');
    $classA = new ClassA($path);
    $classA->method1ClassA(); 
}
```

## その他

### 無名クラスを使ってトレイトをテスト
トレイトとは  
https://www.php.net/manual/ja/language.oop5.traits.php  
PHPのような単一継承言語でコードを再利用するための仕組みの一つ。  
トレイトは単にいくつかの機能をまとめるためだけのもの。  
トレイト自身のインスタンスを作成することはできない。  

```php {hl_lines=["14-22"]}
use Aaa\ClassA;
use Ttt\TraitA;
 
// Omitted
 
/**
 * Test a trait using an anonymous class
 *
 * @covers Aaa\ClassA::method1ClassA
 * @return void
 */
public function testSampleMethod(): void
{
    $classUsingTrait = new class($property) {
        use TraitA;
 
        public function __construct($property)
        {
             $this->method1TraitA($property);
        }
    };
    $classUsingTrait->method2TraitA();
}
```

## References

* PHPUnit のイディオム https://qiita.com/ngmy/items/08af9f74f6308ef6281c
* PHPUnit のアノテーション https://phpunit.readthedocs.io/ja/latest/annotations.html
* Mockery のクラス指定 https://qiita.com/prex-uchida/items/7563f7f33153da463802
* Mockery https://qiita.com/ttn_tt/items/a35638ab12d4dc2ea488
* Mockery（モック化用ライブラリ） https://github.com/mockery/mockery
* vfsStream（ファイル操作用ライブラリ） https://github.com/bovigo/vfsStream
* Trait https://www.php.net/manual/ja/language.oop5.traits.php