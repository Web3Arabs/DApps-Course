# مقدمة في لغة Solidity

Solidity هي لغة برمجة مكتوبة بشكل ثابت ومتعرجة مصممة لتطوير العقود الذكية التي تعمل على Ethereum blockchain. تم تطويره بواسطة مشروع Ethereum كبديل للغات البرمجة الأخرى المستندة إلى blockchain مثل Bitcoin Script. تم تصميمه ليكون مشابهًا للغة JavaScript وله العديد من الميزات نفسها، مثل دعم الميراث والمكتبات والأنواع والوظائف المعرفة من قبل المستخدم.

تُستخدم لغة Solidity لكتابة عقود Ethereum الذكية، والتي هي في الأساس أجزاء من التعليمات البرمجية ذاتية التنفيذ يمكن استخدامها لتسهيل المعاملات والاتفاقيات بين الأطراف على شبكة Ethereum. تتم كتابة العقود الذكية في Solidity ويتم تجميعها في رمز ثنائي يتم نشره بعد ذلك على جهاز Ethereum Virtual Machine (EVM).

> الـ EVM هو ما يقوم بتشغيل الكود ويتتبع حالة العقد

## الشكل العام للعقد الذكي:

```solidity
// حدد إصدار المترجم الذي ستستخدمه
// يجب أن يكون إصدار المترجم أكبر من أو يساوي 0.8.17 وأقل من 0.9.0
pragma solidity ^0.8.17;

// HelloWorld قم بإنشاء عقد بإسم
contract HelloWorld {

}
```

قبل البدء يمكنك إختبار الامثلة التي نقوم بتوضيحها على شبكة Ethereum بإستخدام <a href="https://remix.ethereum.org/" target="_blank">منصة Remix مجاناً</a>

## أنواع المتغيرات

هناك 3 أنواع من المتغيرات في Solidity

**❍ متغيرات حالة (State Variables)**

⬘ متغيرات ثابتة يتم تخزينها في blockchain <br/>
⬘ يمكن الوصول إليها من أي عقد <br/>
⬘ يتم الإعلان عنها خارج function

**❍ متغيرات محلية (Local Variables)**

⬘ متغيرات محلية لا يتم تخزينها في blockchain <br/>
⬘ يتم الوصول اليها في داخل الدالة المعلنة عنها فقط

**❍ متغيرات عالمية (Global Variables)**

⬘ لا يتم الإعلان عنها في العقود الذكية <br/>
⬘ توفر إمكانية الوصول إلى معلومات حول blockchain

> نوع المتغيرات يتم تحديده من خلال مكان الإعلان عنها وليس من خلال قيمتها.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

contract Variables {
    /*
    لتقف على عدد صحيح بدون إشارة، مما يعني الأعداد الصحيحة غير السالبة uint تستخدم
    تتوفر أحجام مختلفة. على سبيل المثال:
        - من 0 الى (1 - 2 ** 8) uint8 ال
        - من 0 الى (1 - 2 ** 256) uint256 ال
    أنه يمكن الوصول إلى المتغير داخليًا public تعني كلمة
    بموجب العقد ويمكن أيضًا قراءتها من قبل العالم الخارجي
    */
    uint8 public u8 = 10;
    uint256 public u256 = 600;

    // State بعض المتغيرات التي تخزن في البلوكتشين من النوع
    // تخزن قيمة نصية
    string public text = "Hello";
    // يقوم بتخزين عددًا صحيحًا بدون إشارة
    uint public num = 12345;
    // يقوم بتخزين عددًا سالباً
    int public x = -10;
    // Ethereum يقوم بتخزين عناوين لشبكة
    address public addre = 0x8626f6940E2eb28930eFb4CeF49B2d1F2C9C1199;
    // يقوم بتخزين قيم منطقية
    bool public boo = false;

    // القيم التلقائية
    // Solidity المتغيرات لها قيم إفتراضية في لغة
    uint public defaultUint; // 0
    int public defaultInt; // 0
    bool public defaultBool; // false
    address public defaultAddr; // 0x0000000000000000000000000000000000000000

    function doSomething() public {
        // متغير محلي لا يحفظ في البلوكتشين
        uint i = 456;

        // بعض المتغيرات العالمية
        uint timestamp = block.timestamp; // الطابع الزمني للكتلة الحالية
        address sender = msg.sender; // عنوان المتصل بالعقد
    }
}
```

## أوامر الشرط if/else

يتم استخدام عبارة if/else لتنفيذ كود مختلف اعتمادًا على شرط معين.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Conditions قم بإنشاء عقد بإسم
contract Conditions {
    /**
        foo اسم الوظيفة
        يأخذ قيمة عددية ويعيد قيمة عددية
        if/else يقارن قيمة المتغير باستخدام
    */
    function foo(uint x) public pure returns (uint) {
        // في حالة كان قيمة المتغير اقل من 10 قم بإعادة 0
        if (x < 10) {
            return 0;
        }
        // في حالة كان قيمة المتغير اقل من او يساوي 20 قم بإعادة 1
        else if (x <= 20) {
            return 1;
        }
        // غير ذلك قم بإعادة 2
        else {
            return 2;
        }
    }
}
```

## الحلقات for loop والدوال functions

#### مستويات الدوال (functions) في Solidity:

- **عام (public)**: يمكن لأي عقد ذكي أو حساب/عقد خارجي استدعاء هذه الدالة.
- **خاص (private)**: العقد الذكي الحالي فقط من يمكنه إستدعائه والتفاعل معه ولا يمكن للحسابات/العقود الذكية الخارجية استدعائه او التفاعل معه.
- **خارجي (external)**: لا يمكن استدعاؤه داخليًا عن طريق العقد الذكي الحال، ويمكن إستدعائه بواسطة العقود الذكية الأخرى أو الحسابات خارجية.
- **داخلي (internal)**: العقد الحالي أو العقد المشتق لك من يمكنه إستدعائه والتفاعل معه.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Example قم بإنشاء عقد بإسم
contract Example {
    // State متغير يقوم بتخزين قيمة عددية نوعه
    uint public num;

    /**
        set دالة تحمل الاسم
        _num تأخذ عدداً صحيحاً بدون إشارة
        تم إعلانه كمعنى دالة عامة
        يمكن استدعاؤه من داخل العقد وكذلك خارجيًا.
        تم إنشائه كدالة عامة
        يمكن استدعاؤه من داخل العقد وكذلك خارجيًا لأنه عام
    */
    function set(uint _num) public {
        num = _num;
    }

    /**
        get دالة تحمل الاسم
        تقوم بإعادة عدداً صحيحاً بدون إشارة
        (view) تم إعلانه كمعنى دالة عامة وعرض
        أن الدوال لا تغير حالة أي متغير
        تم إنشائه كدالة عامة
        "view" الدوال التي يكون الغرض منها فقط قرأة متغير بحيث لا نغير من قيمته/حالته نجعله 
    */
    function get() public view returns (uint) {
        return num;
    }

    /**
        loop دالة تحمل الاسم
        تقوم بتشغيل حلقة دوران
        تحمل أيضاً شروط معينة اثناء عملية الدوران
    */
    function loop() public {
        // for حلقة دوران
        for (uint i = 0; i < 20; i++) {
            // الشروط
            if (i == 10) {
                // انتقل إلى التكرار التالي بالمتابعة
                continue;
            }
            if (i == 14) {
                // حلقة الخروج مع التوقف
                break;
            }
        }
    }
}
```

## التعيين Mapping

تعمل Mappings في Solidity مثل hashmaps أو القواميس في لغات البرمجة الأخرى. يتم استخدامها لتخزين البيانات في أزواج ذات قيمة مفتاح.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Mapping قم بإنشاء عقد بإسم
contract Mapping {
    // uint إنشاء تعيين من العنوان الى قيمة
    mapping(address => uint) public myMap;

    function get(address _addr) public view returns (uint) {
        // تقوم دائماً بإرجاع قيمة
        // إذا لم يتم تعيين القيمة مطلقًا، فستُرجع القيمة الافتراضية
        return myMap[_addr];
    }

    function set(address _addr, uint _i) public {
        // معين (address) قم بتحديث القيمة المتعلقة بحساب
        myMap[_addr] = _i;
    }

    function remove(address _addr) public {
        // أعد تعيين القيمة الحالية إلى القيمة الافتراضية
        delete myMap[_addr];
    }
}
```

## المصفوفات Arrays

تعمل المصفوفات في Solidity بنفس الطريقة التي تعمل بها في لغات البرمجة الأخرى.

```solidity
// حدد إصدار المترجم الذي ستستخدمه
pragma solidity ^0.8.17;

// Arrays قم بإنشاء عقد بإسم
contract Arrays {

    // uint نقوم بإنشاء مصفوفة نوعها
    uint[] public numbers;
    // يمكننا إنشاء مصفوفة ذات حجم ثابت، يتم رتيب جميع القيم في اماكن/خانات في الذاكرة بدايةً من 0
    uint[10] public mySizeArr;

    // أضف قيمة إلى نهاية المصفوفة
    function addNumber(uint _number) public {
        numbers.push(_number);
    }

    // استرجع قيمة من المصفوفة في خانة/مكان معين
    function getNumber(uint _index) public view returns (uint) {
        return numbers[_index];
    }

    // حذف قيمة من المصفوفة في خانة/مكان معين
    function deleteNumber(uint _index) public {
        delete numbers[_index];
    }

    // ".length" معرفة طول المصفوفة باستخدام
    function getStuf() public view returns (uint) {
        return mySizeArr.length; // 10
    }
}
```

يمكنك الانتقال الان الى الدرس التالي ومعرفة المزيد حول اساسيات لغة Solidity 🚀

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
