
هاك كودجن
المستندات
بداية سريعة
تثبيت

تم تثبيت Hack Codegen مع Composer :
1
$ composer require facebook/hack-codegen
الالتزام بمخرجات Codegen الخاصة بك

في اللغات الأخرى، من الأفضل عادةً عدم الالتزام بالعناصر التي تم إنشاؤها؛ هذا ليس هو الحال بالنسبة إلى كود الاختراق الذي تم إنشاؤه، حيث أن عدم الالتزام بالمخرجات يؤدي إلى إنشاء تبعية دائرية:
إذا أبلغ مدقق الكتابة Hack عن أي أخطاء، فسيقوم HHVM بإصدار خطأ فادح عند تنفيذ أي رمز Hack
تعتبر الإشارات إلى الفئات والوظائف والأنواع غير المحددة بمثابة خطأ في مدقق الكتابة
أي إشارات إلى الكود الذي تم إنشاؤه ستكون مراجع غير محددة عند الخروج الجديد
نظرًا لأن Hack Codegen نفسه مكتوب في Hack، فسوف يظهر خطأ فادح بواسطة HHVM عندما تحاول إنشاء التعليمات البرمجية الخاصة بك.
بالإضافة إلى ذلك، يعد هذا مطلوبًا لدعم الملفات التي تم إنشاؤها جزئيًا.
مثال بسيط

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
<?hh
require_once('vendor/autoload.php');

use Facebook\HackCodegen\{
  CodegenFileType,
  HackCodegenConfig,
  HackCodegenFactory,
  HackBuilderValues
};

function make_script(): void {
  $cg = new HackCodegenFactory(
    new HackCodegenConfig(__DIR__),
  );

  $cg->codegenFile('script.php')
    ->setFileType(CodegenFileType::HACK_PARTIAL)
    ->setShebangLine('#!/usr/bin/env hhvm')
    ->setPseudoMainHeader(
      'require_once("vendor/autoload.php");',
    )
    ->addFunction(
      $cg->codegenFunction('main')
        ->setReturnType('void')
        ->setBody(
          $cg->codegenHackBuilder()
            ->startManualSection('greeting')
            ->addAssignment(
              '$greeting',
              "Hello, world!",
              HackBuilderValues::export(),
            )
            ->endManualSection()
            ->addLine(
              'printf("%s\n", $greeting);'
            )
            ->getCode(),
        ),
    )
    ->setPseudoMainFooter(
      'main();',
    )
    ->save();
}

make_script();
هذا يولد:
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
#!/usr/bin/env hhvm
<?hh
/**
 * This file is partially generated. Only make modifications between BEGIN
 * MANUAL SECTION and END MANUAL SECTION designators.
 *
 * @partially-generated SignedSource<<484789e339a716fbb2ed2385d32b6b75>>
 */
require_once("vendor/autoload.php");

function main(): void {
  /* BEGIN MANUAL SECTION greeting */
  $greeting = 'Hello, world!';
  /* END MANUAL SECTION */
  printf("%s\n", $greeting);
}

main();
يمكن إجراء تغييرات بين MANUAL SECTIONالعلامات، وسيتم الاحتفاظ بها بغض النظر عن أي تغييرات في إنشاء التعليمات البرمجية لبقية الملف.
تحرير على جيثب
الفيسبوك مفتوح المصدر
مشاريع مفتوحة المصدر جيثب تويتر
المساهمة في هذا المشروع على جيثب
