# اساسيات لغة Solidity

## تعداد Enums

كلمة Enum تعني **Enumerable**. يتم استخدامها بشكل شائع لتقييد المتغير بحيث يكون له قيمة واحدة فقط من عدد قليل من القيم المحددة مسبقًا. نظرًا لأنها مجرد تجريد للثوابت التي يمكن قراءتها من قبل الإنسان، في الواقع، يتم تمثيلها داخليًا على أنها uints.

Enum هو نوع بيانات محدد من قبل المستخدم يقيد متغيرًا بحيث يكون له قيمة واحدة فقط من بين عدد قليل من القيم المحددة مسبقًا. تسمى القيم الموجودة في هذه القائمة التي تم تعدادها بالتعدادات، ويمكن استخدامها لنمذجة الاختيار وتتبع الحالة. يمكن الإعلان عن Enums خارج العقد ويمكن تحويلها صراحةً من وإلى جميع أنواع الأعداد الصحيحة، لكن التحويل الضمني غير مسموح به. القيمة الافتراضية للتعداد هي القيمة التي ينتجها التعبير.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Enums قم بإنشاء عقد بإسم
contract Enums {
    // Enum تمثل حالة النقل
    enum Status {
        Pending,
        Shipped,
        Accepted,
        Rejected,
        Canceled
    }

    // "Pending" القيمة الافتراضية هي العنصر الأول المدرج في تعريف النوع في هذه الحالة
    Status public status;

    // uint يعيد قيم من النوع
    // Pending  = 0
    // Shipped  = 1
    // Accepted = 2
    // Rejected = 3
    // Canceled = 4
    function get() public view returns (Status) {
        return status;
    }

    // في الدالة uint تحديث الحالة عن طريق ادخال قيمة من النوع
    function set(Status _status) public {
        status = _status;
    }

    // الى قيمته الاساسية وهي 0 enum يعيد قيمة
    function deleteEmum() public {
        delete status;
    }
}
```

## الهياكل Structs

الهياكل في Solidity هي طريقة لتعريف الأنواع المخصصة مع أعضاء من الأنواع الأخرى. إنها مفيدة لتجميع البيانات ذات الصلة معًا، مثل عناوين الكتب والمؤلفين وأرقام ISBN في مكتبة. يمكن الإعلان عن الهياكل خارج العقد ويمكن تحويلها صراحةً من وإلى جميع أنواع الأعداد الصحيحة، لكن التحويل الضمني غير مسموح به. يمكن أيضًا استخدام البنى لتخزين أنواع البيانات المعقدة مثل التعيينات والمصفوفات. بالإضافة إلى ذلك، يمكن تخزين الهياكل في هياكل أخرى، مما يسمح بتداخل البيانات.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// SampleStruct قم بإنشاء عقد بإسم
contract SampleStruct {

    // قم بتعريف البنية التي تجمع بين ثلاثة أنواع من البيانات
    struct Employee {
        string name;
        uint age;
        uint salary;
    }

    // Employee قم بإنشاء مصفوفة من
    Employee[] public employees;

    // Employee قم بإدخال البيانات التي تريد إضافتها الى
    function addEmployee(string memory _name, uint _age, uint _salary) public {
        Employee memory newEmployee = Employee(_name, _age, _salary);
        employees.push(newEmployee);
    }

    // Employee قم بإعادة البيانات حسب الموقع الخاص به في
    function getEmployee(uint index) public view returns (string memory, uint, uint) {
        Employee storage employee = employees[index];
        return (employee.name, employee.age, employee.salary);
    }
}
```

## View and Pure Functions

هي وظائف يتم الإعلان عنها باستخدام مُعدِّلات معينة تفرض قيودًا معينة على سلوكها. وظيفة View للقراءة فقط، مما يعني أنها لا تستطيع تعديل حالة blockchain. يتم استخدامها للحصول على البيانات من blockchain ، مثل رصيد الحساب.

بينما pure function لا تقرأ الوظائف البحتة حالة blockchain أو تعدلها، ولكن يُسمح لها باستخدام المتغيرات المحلية المعلنة داخل الوظيفة. تُستخدم pure function بشكل عام للحسابات التي لا تحتاج إلى التفاعل مع blockchain.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Test قم بإنشاء عقد بإسم
contract Test {
    uint public y = 1;

    // لأنها لا تعدل حالة اي شيئ ولا تستخدم أي مصادر بيانات خارجية pure هذه الدالة تم إستخدام
    function addPure(uint256 i, uint256 j) public pure returns (uint256 result) {
        return i + j;
    }

    // لأنها لا تعدل حالة اي شيئ تستخدم أي مصادر بيانات خارجية view هذه الدالة تم إستخدام
    function addView(uint x) public view returns (uint256 balance) {
        return x + y;
    }
}
```

## معدِّلات الدالة Function Modifier

تُستخدم معدِّلات الدالة لتعديل سلوك الدالة في العقد الذكي. يمكن استخدامها للتحقق تلقائيًا من حالة قبل تنفيذ الدالة، أو لتقييد الوصول إلى الدالة بناءً على شروط معينة. على سبيل المثال، يمكن استخدام مُعدِّل للتحقق من تشغيل دالة بواسطة مستخدم معين فقط، أو للتأكد من أن الدالة يتم تنفيذها فقط عند استيفاء شرط معين.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Example قم بإنشاء عقد بإسم
contract Example {
    address public owner = msg.sender;

    /**
        تم استخدام معدِّل المالك الوحيد لتقييد الوصول
        بحيث لا يستطيع احد الوصول ان لم يكن هو المالك
    */
    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }

    /**
        لسحب الاموال في العقد withdraw يتم استخدام دالة
        وفي هذه الطريقة لن يستطيع احد سحب الاموال من العقد الا المالك
    */
    function withdraw() external onlyOwner() {
        // withdraw code here
    }
}
```

## الأحداث Events

الأحداث في Solidity هي واجهات مع وظائف تسمح للعقود بإجراء تسجيل الدخول على Ethereum blockchain. يتم استخدامها لإرجاع القيم من المعاملات ولتسجيل الإخطارات حول التغييرات أو الإجراءات التي تحدث في العقد. توفر الأحداث وسيلة للتطبيقات الخارجية للاستماع إلى أحداث معينة وقعت على Ethereum blockchain. تحتوي الأحداث على ما يصل إلى ثلاث معلمات يمكن فهرستها، مما يسمح للتطبيقات الخارجية بالاستعلام عن البيانات التي تم تسجيلها بسهولة.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Event قم بإنشاء عقد بإسم
contract Event {
    /**
        إعلان الحدث
        يمكن فهرسة ما يصل إلى 3 معلمات
        تساعدك المعلمات المفهرسة على تصفية السجلات حسب المعلمة المفهرسة
    */
    event Try(address indexed sender, string message);

    function test() public {
        emit Try(msg.sender, "Hello World!");
        emit Try(msg.sender, "Hello EVM!");
    }
}
```

## Constructor

هو وظيفة خاصة تُستخدم لتهيئة متغيرات الحالة في العقد. يتم استدعاء constructor عند نشر العقد لأول مرة في blockchain ويمكن استخدامه لتعيين القيم الأولية لمتغيرات العقد. يمكن لل constructor أيضًا أخذ الحجج التي يمكن استخدامها لتخصيص الحالة الأولية للعقد بشكل أكبر.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Example قم بإنشاء عقد بإسم
contract Example {
    string public name;

    constructor(string memory _name) {
        name = _name;
    }
}
```

## الميراث Inheritance

هو وسيلة لتوسيع وظائف العقد. يسمح للمبرمج بإنشاء عقد يرث جميع سمات وخصائص عقد آخر، والتي يمكن استخدامها لإنشاء عقود أكثر تعقيدًا. تدعم Solidity كلاً من الميراث الفردي والمتعدد، كما توفر القدرة على تجاوز الوظائف من العقد الأساسي. بالإضافة إلى ذلك، تدعم Solidity تعدد الأشكال، والذي يسمح لاستدعاء الوظيفة بتنفيذ وظيفة الفئة الأكثر اشتقاقًا دائمًا.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// A قم بإنشاء عقد بإسم
contract A {
    uint256 public a;

    constructor() public {
        a = 5;
    }

    function setA(uint256 _a) public virtual {
        a = _a;
    }
}

// A يرث من العقد B قم بإنشاء عقد بإسم
contract B is A {
    uint256 public b;

    constructor() public {
        b = 10;
    }

    function setB(uint256 _b) public {
        b = _b;
    }

    function setA(uint256 _a) public virtual override {
        a = _a * 2;
    }
}
```

في المثال السابق، يرث العقد B من العقد A. وهذا يعني أن العقد B لديه حق الوصول إلى المتغيرات والوظائف المحددة في العقد A. يمكن للعقد B أيضًا تجاوز دالة ()setA من العقد A، بحيث عندما يتم استدعاؤها في العقد B، ستكون النتيجة ضعف القيمة التي تم تمريرها في constructor.

## تحويل ETH

**❍ كيف ترسل Ethereum؟**

يمكنك إرسال Ethereum الى العقود الذكية بواسطة:

⬘ transfer <br/>
⬘ send <br/>
⬘ call

**❍ كيف تلقي Ethereum؟**

يمكنك تلقي Ethereum بواسطة:

⬘ receive() external payable <br/>
⬘ fallback() external payable

**ماهو الافضل للاستخدام؟**

يعد استخدام call هي الطريقة الموصى بها للاستخدام بعد ديسمبر 2019.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Ether غرضه إرسال SendEther قم بإنشاء عقد بإسم
contract SendEther {

    function sendEther(address payable _to) public payable {
        // بإرجاع قيمة منطقية تشير إلى النجاح أو الفشل call يقوم
        (bool sent, bytes memory data) = _to.call{value: msg.value}("");
        require(sent, "Failed to send Ether");
    }
}

// Ether غرضه تلقي ReceiveEther قم بإنشاء عقد بإسم
contract ReceiveEther {
    /*
            send Ether
                |
            msg.data is empty?
                / \
              yes  no
               /     \
  receive() exists?  fallback()
            /   \
            yes   no
            /      \
        receive()   fallback()
    */

    // فارغة msg.data دالة لتلقي الأثير, يجب أن تكون
    receive() external payable {}

    // فارغة msg.data يتم استدعاء الوظيفة الاحتياطية عندما لا تكون
    fallback() external payable {}

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
```

## استدعاء عقد آخر Calling Other Contract

يمكن للعقود استدعاء عقود أخرى فقط عن طريق استدعاء وظائف على مثيل من العقد الآخر مثل A.foo(x، y، z). للقيام بذلك، يجب أن يكون لديك contract لـ A تخبر عقدك بالوظائف الموجودة.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Callee قم بإنشاء عقد بإسم
contract Callee {
    uint public x;
    uint public value;

    function setX(uint _x) public returns (uint) {
        x = _x;
        return x;
    }
}

// Caller قم بإنشاء عقد بإسم
contract Caller {
    function setX(Callee _callee, uint _x) public {
        uint x = _callee.setX(_x);
    }

    function setXFromAddress(address _addr, uint _x) public {
        Callee callee = Callee(_addr);
        callee.setX(_x);
    }
}
```

## استدعاء العقود الخارجية Calling External Contracts

يمكن للعقود استدعاء عقود أخرى فقط عن طريق استدعاء وظائف على مثيل من العقد الآخر مثل A.foo(x، y، z). للقيام بذلك، يجب أن يكون لديك واجهة لـ A تخبر عقدك بالوظائف الموجودة. تتصرف الواجهات في Solidity مثل ملفات الرأس، وتخدم أغراضًا مماثلة لـ ABI التي استخدمناها عند استدعاء العقود من الواجهة الأمامية. يسمح هذا للعقد بمعرفة كيفية تشفير وسيطات الدالة وفك تشفيرها وإرجاع القيم لاستدعاء العقود الخارجية.

> لا يلزم أن تكون الواجهات التي تستخدمها واسعة النطاق. أي أنها لا تحتاج بالضرورة إلى احتواء جميع الوظائف الموجودة في العقد الخارجي - فقط تلك التي قد تطلبها في وقت ما.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Counter قم بإنشاء عقد بإسم
contract Counter {
    uint public count;

    function increment() external {
        count += 1;
    }
}

// ICounter قم بإنشاء واجهة بإسم
interface ICounter {
    function count() external view returns (uint);

    function increment() external;
}

// MyContract قم بإنشاء عقد بإسم
contract MyContract {
    function incrementCounter(address _counter) external {
        ICounter(_counter).increment();
    }

    function getCount(address _counter) external view returns (uint) {
        return ICounter(_counter).count();
    }
}
```

## الإستيراد Import

**محلياً**

قم بإنشاء ملفان بنفس هذه الصيغة
```bash
├── Import.sol
└── Foo.sol
```

قم بلصق هذا في ملف Foo.sol

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Foo قم بإنشاء عقد بإسم
contract Foo {
    string public name = "Foo";
}
```

قم بلصق هذا في ملف Import.sol

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Foo.sol إستدعاء ملف
import "./Foo.sol";

// Import قم بإنشاء عقد بإسم
contract Import {

    Foo public foo = new Foo();

    function getFooName() public view returns (string memory) {
        return foo.name();
    }
}
```

**خارجياً**

يمكنك أيضًا الاستيراد من <a href="https://github.com" target="_blank">GitHub</a> ببساطة عن طريق نسخ عنوان url

```solidity
// https://github.com/owner/repo/blob/branch/path/to/Contract.sol
import "https://github.com/owner/repo/blob/branch/path/to/Contract.sol";
```

## المكتبات Libraries

عبارة عن كتل من التعليمات البرمجية التي يمكن إعادة استخدامها والتي يمكن استدعاؤها من عقود أخرى.

لاستخدام مكتبة في Solidity، يجب عليك أولاً نشرها في blockchain. ستبقى المكتبة على blockchain وستكون في متناول جميع العقود. لاستدعاء دالة مكتبة من عقد، يمكنك استخدام الكلمة المفتاحية للمكتبة متبوعة بعنوان المكتبة واسم الدالة.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Math قم بإنشاء مكتبة بإسم
library Math {
    function sqrt(uint y) internal pure returns (uint z) {
        if (y > 3) {
            z = y;
            uint x = y / 2 + 1;
            while (x < z) {
                z = x;
                x = (y / x + x) / 2;
            }
        } else if (y != 0) {
            z = 1;
        }
    }
}

// TestMath قم بإنشاء عقد بإسم
contract TestMath {
    function testSquareRoot(uint x) public pure returns (uint) {
        return Math.sqrt(x);
    }
}
```

### بعض الموارد التي ستساعدك في تعلم Solidity:

- <a href="https://solidity-by-example.org/" target="_blank">Solidity by Example</a>
- <a href="https://docs.soliditylang.org/" target="_blank">وثائق Solidity</a>

لقد انتهينا الى هنا!! يمكنك البدء في بناء تطبيقات DApps الان 🚀

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">Telegram</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
