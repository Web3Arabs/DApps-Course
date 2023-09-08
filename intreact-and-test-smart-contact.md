# التفاعل مع العقد الذكي وإجراء إختبار عليه باستخدام Hardhat

## التفاعل مع العقد الذكي باستخدام Hardhat

في الدرس السابق تمكنا من بناء عقد ذكي ونشره على شبكة sepolia. ولكن اثناء بناء عقود ذكية فنحن بالحاجة بشكل مستمر بتجربة العقد الذكي الذي انتهينا من كتابته فلذلك في هذا الدرس سوف تعرف كيف تقوم بإختبار عقدك الذكي بشكل مستمر على شبكة التطوير بإستخدام Hardhat.

### المتطلبات الاساسية للبدء في هذا الدرس:

1. انتهيت من قراءة درس "<a href="https://www.web3arabs.com/courses/d64bee08-2e38-4ad5-958e-5ab6c42ebb41/lessons/f426338c-23db-463f-9f6a-74a4d9c02b91" target="_blank">إنشاء عقد ذكي لمشروع Todo-list</a>"
2. يمكنك التعامل مع لغة البرمجة JavaScript.

> سنقوم بإستخدام نفس المشروع السابق وفي نفس تطبيق hardhat الذي قمنا ببناء العقد الذكي من خلاله.

قم بفتح مجلد scripts وانشئ ملف باسم todo-list.js

```javascript
const hre = require("hardhat")

// وظيفة تعمل على استدعاء المهامات من العقد الذكي
function get_tasks(contract, user) {
  return contract.connect(user).getTasks()
}

async function main() {
  // احصل على بعض الحسابات التي سنعمل معها
  const [user1] = await hre.ethers.getSigners()

  // نحصل على العقد للنشر
  const TodolistContract = await hre.ethers.getContractFactory("Todolist")
  const todolistContract = await TodolistContract.deploy()

  // نشر العقد
  await todolistContract.deployed()
  console.log("Todolist contract deployed to:", todolistContract.address)

  // إضافة مهمة للعقد
  await todolistContract.connect(user1).addTask("Learn Web3")
  await todolistContract.connect(user1).addTask("Travel")
  await todolistContract.connect(user1).addTask("Write")
  console.log("Added tasks!")

  // الحصول على المهمات من العقد المتعلقة بهذا المستخدم
  console.log("Your tasks: ", await get_tasks(todolistContract, user1))

  // تحديث حالة بعض المهام
  console.log("Updateing your tasks...")
  await todolistContract.connect(user1).updateStatus(0)
  await todolistContract.connect(user1).updateStatus(2)
  console.log("Updated! your tasks: ", await get_tasks(todolistContract, user1))

  // حذف بعض المهام
  console.log("Deleting your tasks...")
  await todolistContract.connect(user1).deleteTask(0)
  await todolistContract.connect(user1).deleteTask(2)
  console.log("Deleted! your tasks: ", await get_tasks(todolistContract, user1))
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  })
```

<img src="https://www.web3arabs.com/courses/dapps/todolist/intreact-contract.png"/>

يقوم الكود السابق بتجربة العقد الخاص بنا بشكل كامل, دعنا نفهم ماتم كتابته...

```javascript
// احصل على بعض الحسابات التي سنعمل معها
const [user1] = await hre.ethers.getSigners()

// نحصل على العقد للنشر
const TodolistContract = await hre.ethers.getContractFactory("Todolist")
const todolistContract = await TodolistContract.deploy()

// نشر العقد
await todolistContract.deployed()
console.log("Todolist contract deployed to:", todolistContract.address)
```

لقد قمنا أولاً بإستدعاء الدالة "()getSigners" من "ethers" للحصول على مجموعة من المُوقِّعين من مثيل "Hyperledger Ressource Environment" وتخزينها في متغيرات ويمكننا جلب اكثر من توقيع عن طريق عمل "[user1, user2, user3]" ومن ثم قمنا بجلب العقد الذكي "Todolist" وفي الاخير قمنا بنشر هذا العقد الذكي.

```javascript
// إضافة مهمة للعقد
await todolistContract.connect(user1).addTask("Learn Web3")
await todolistContract.connect(user1).addTask("Travel")
await todolistContract.connect(user1).addTask("Write")
console.log("Added tasks!")
```

لقد قمنا باستدعاء العقد الذكي الخاص بنا الذي قمنا بنشره سابقاً وربطنا العقد الذكي بالموقع "user1" ومن ثم استدعينا احد الوظائف التي قمنا بإنشأها في العقد الذكي "(name_)addTask" والتي تعمل على إضافة المهام وادخلنا اسم المهمة الى هذه الوظيفة.

```javascript
// وظيفة تعمل على استدعاء المهامات من العقد الذكي
function get_tasks(contract, user) {
  return contract.connect(user).getTasks()
}
```

لقد قمنا بإنشاء وظيفة لكي تقوم بجلب المهام من العقد الذكي لكي نتمكن من تكرارها اكثر من مرة ورؤية التحديثات في كل مرة نجري بها تعديلات على العقد الذكي.

```javascript
// تحديث حالة بعض المهام
console.log("Updateing your tasks...")
await todolistContract.connect(user1).updateStatus(0)
await todolistContract.connect(user1).updateStatus(2)
console.log("Updated! your tasks: ", await get_tasks(todolistContract, user1))

// حذف بعض المهام
console.log("Deleting your tasks...")
await todolistContract.connect(user1).deleteTask(0)
await todolistContract.connect(user1).deleteTask(2)
console.log("Deleted! your tasks: ", await get_tasks(todolistContract, user1))
```

لقد اجرينا أولاً تحديث للمهمة التي تحمل الموقع 0 والمهمة التي تحمل الموقع 2 والتي سيجعل المهمة من مهمة لم يتم تنفيذها "FALSE" الى مهمة تم تنفيذها "TRUE" ومن ثم نقوم بجلب المهام وطباعتها لكي نتمكن من رؤية ما حدث.

ثم قمنا بحذف المهمة التي تحمل الموقع 0 والمهمة التي تحمل الموقع 2 والذي سيقوم بإزالة هذه المهام ومن ثم قمن بجلب المهام مجدداً وطباعتها.

بعد ان قمنا بإستدعاء جميع الوظائف التي قمنا بإنشأها في العقد الذكي دعنا نرئ ما الذي سيحدث عند تجربة هذا

```bash
npx hardhat run scripts/todo-list.js
```

<img src="https://www.web3arabs.com/courses/interactiong-use-hardhat-results.png"/>

إنه يعمل بشكل جيد! 🥳

## إجراء عملية اختبار للعقد الذكي باستخدام Hardhat

تعد كتابة الاختبارات الآلية عند إنشاء العقود الذكية أمرًا ضروريًا للتأكد من أن الكود آمن ويعمل بشكل صحيح  هذا مهم بشكل خاص عندما يتعامل المستخدمون مع الأموال، حيث يمكن أن تؤدي أي ثغرات أو ثغرات أمنية إلى تعرض أموالهم للخطر. يمكن أن تساعد الاختبارات الآلية في اكتشاف مثل هذه المشكلات قبل نشرها، مما يساعد على حماية أموال المستخدمين.

لاختبار عقدنا، سنستخدم شبكة Hardhat  وهي شبكة Ethereum محلية مصممة للتطوير. يأتي مدمجًا مع Hardhat ،ويتم استخدامه كشبكة افتراضية. لا تحتاج إلى إعداد أي شيء لاستخدامه.

قم بفتح مجلد test وأنشئ ملف بإسم Todolist.js وقم بنسخ ولصق هذا الى الملف.

```javascript
const { expect } = require("chai")
const { ethers } = require("hardhat")

/**
  تعمل على تجميع الاختبارات ذات الصلة معًا. تأخذ سلسلة كمعاملها الأول واستدعاء اختياري كوسيطة ثانية لها.
  يساعد أيضاً على إبقائها منظمة ويسهل قراءة الاختبارات وفهمها،حيث يوفر كل وصف بعض السياق للاختبارات التي تليها.
*/
describe("Todolist Contract", async function () {
  // تخزين معلومات العقد الذكي بعد النشر
  let todolist

  // قبل اي شيئ, سيتم أولاً نشر العقد الذكي
  beforeEach(async function() {
    // جلب العقد الذكي للنشر
    const Todolist = await ethers.getContractFactory("Todolist")
    todolist = await Todolist.deploy()
    // نشر العقد
    await todolist.deployed()
  })

  // إجراء إختبار لدالة إضافة مهمة و جلب المهام
  it('should add a task' , async function() {
    // اضافة مهمة جديدة
    await todolist.addTask("Learn Test")
    // جلب المهام
    const tasks = await todolist.getTasks()
    // "Learn Test" التوقع بأن المهمة التي في الموقع 0 تساوي (تحتوي) على الاسم
    expect(tasks[0].name).to.equal("Learn Test")
  })

  // إجراء إختبار لدالة تحديث حالة المهمة و جلب المهام
  it('should update status of a task', async function() {
    // اضافة مهمة جديدة
    await todolist.addTask("Learn Test")
    // "true" تحديث حالة المهمة التي في الموقع 0 الى
    await todolist.updateStatus(0)
    // جلب المهام
    const tasks = await todolist.getTasks()
    // "true" التوقع بأن حالة المهمة التي في الموقع 0 هي
    expect(tasks[0].status).to.equal(true)
  })

  // إجراء إختبار لدالة إزالة مهمة و جلب المهام
  it('should delete a task', async function() {
    // اضافة مهمة جديدة
    await todolist.addTask("Learn Test")
    // حذف المهمة التي في الموقع 0
    await todolist.deleteTask(0)
    // جلب المهام
    const tasks = await todolist.getTasks()
    // التوقع بأن اسم المهمة التي في الموقع 0 قد اصبح فارغ بعد الإزالة
    expect(tasks[0].name).to.equal('')
  })
})
```

<img src="https://www.web3arabs.com/courses/dapps/todolist/test-contract.png"/>

تُستخدم **()describe** في اختبارات Hardhat لتجميع الاختبارات ذات الصلة معًا. تأخذ سلسلة كمعاملها الأول واستدعاء اختياري كوسيطة ثانية لها. يتم استخدام رد الاتصال لتحديد الاختبارات التي سيتم تجميعها تحت الوصف المحدد. يساعد وصف الاختبارات على إبقائها منظمة ويسهل قراءة الاختبارات وفهمها، حيث يوفر كل وصف بعض السياق للاختبارات التي تليها.

تُستخدم **()it** في اختبارات Hardhat لتحديد اختبار واحد. تأخذ سلسلة كمعاملها الأول ونداء كوسيطة ثانية لها. يحتوي رد الاتصال على الكود الذي سيتم تنفيذه عند تشغيل الاختبار، ويجب أن يحتوي على تأكيدات للتحقق من السلوك المتوقع. يجب أن توفر السلسلة اسمًا وصفيًا للاختبار، حيث سيساعد ذلك في تسهيل قراءة الاختبارات وفهمها.

قم بفتح المشروع في terminal وقم بكتابة هذا

```bash
npx hardhat test
```

<img src="https://www.web3arabs.com/courses/tests-use-hardhat-results.png"/>

إنه يعمل بشكل جيد! 🥳

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">Telegram</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
