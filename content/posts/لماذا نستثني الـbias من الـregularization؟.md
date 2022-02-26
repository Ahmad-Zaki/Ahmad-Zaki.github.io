---
title: "؟Regularizationمن الـ Biasلماذا نستثني الـ"
subtitle: "ولماذا نحتاجه في الموديل"
#description: <descriptive text here>
date: 2021-11-30T16:46:33+02:00
draft: false
toc: false
image: ""
featuredImage: ""
featuredImagePreview: "lib/img/linear regression with bias term (right) vs with no bias term (left).JPG"
tags: [Machine Learning, Bias, Regularization, Linear Regression]
categories: [مما تعلمت]
---

<div dir="rtl" style="text-align: justify">
    <h4>
        الـregularization هو أحد الحلول الشائعة لمشكلة الـoverfitting، بيتم عن طريق إضافة penalty مباشرة على الـparameters في الـcost function عشان أقلل الـmodel complexity، لكن لو لاحظت هتلاقي إن الـbias مش موجود في الـpenalty دى على الرغم من إنه أحد الـparameters الى الموديل بيتعلمها، ايه السبب؟
    </h4>
</div>

<!--more-->

<p align="center">
    <img src="/lib/img/linear regression with bias term (right) vs with no bias term (left).JPG" alt="Effect of adding bias on a linear regression model"/>
    <br>
    Fig 1: Linear regression with bias term (right) vs without bias term (left).
    <br> 
    Source: <a href="https://www.deepwizai.com/simply-deep/why-does-regularizing-the-bias-lead-to-underfitting-in-neural-networks">Click here</a> 
</p>

<div dir="rtl" style="text-align: justify">
    <h3>
        خلينا نبدأ من ورا شوية: إحنا ليه بنحتاج bias term ؟
    </h3>
    <h4>
        لإن الـbias بيمنح الموديل حرية أكبر في الحركة والتعلم، لاحظ Figure 1: في الحالتين استخدمت linear regression ، لكن على اليمين وصلت لموديل جيد جداً والـerror فيه قليل، أما على اليسار أستخدمت نفس الموديل لكن بدون bias وكانت النتيجة بعيدة تماماً عن الـdata، لإن لما بحط الـbias بصفر كده أنا أجبرت الموديل يمر بنقطة الأصل، وبالتالي ربطته بحالة واحدة بس في الغالب هتكون غير مناسبة للداتا الى معايا.
    </h4>
    <h4>
        الملاحظة التانية هى إن وجود الـbias أو عدمه مغيرش في الـmodel complexity على الإطلاق، في الحالتين الخط المستقيم متغيرش، الإختلاف الوحيد هو مكانه، ده معناه إن الـbias مش بيساهم في زيادة تعقيد الموديل، لكن بيمنحه حرية بيحتاجها في أوقات كتير، والميزة كمان إن ممكن الموديل بنفسه يتعلم إن الـbias صفر لو مش هيحتاجه.
    </h4>
    <h3>
        طيب إيه الى هيحصل لو عملت regularization للـbias؟
    </h3>
    <h4>
        الـregularization بيجبر كل الـparameters إنها تاخد قيم صغيرة (في حالة الـL1 regularization ممكن يخليها بصفر كمان) بهدف إنه يقلل الـpenalty الإضافية ويوصل لـminimum cost، بالتالي لو ضفت الـbias معاهم هيكون مضطر يحطله قيم صغيرة جداً، وده عكس الى انا كنت عايزه لما ضفت الـbias: أنا كده سحبت منه الحرية الى كنت عايز اديهاله لما ضفت الـbias من البداية! والنتيجة إن في الغالب هتلاقي underfitting كبير في الموديل (لاحظ Figure 2).
    </h4>
</div>

<p align="center">
    <img src="/lib/img/Regularized biad vs unregularized bias.jpg" alt="Regularized biad vs unregularized bias"/>
    <br>
    Fig 2: linear regression model with regularized bias vs with unregularized bias. 
    <br> 
    Source: <a href="https://www.deepwizai.com/simply-deep/why-does-regularizing-the-bias-lead-to-underfitting-in-neural-networks">Click here</a> 
</p>

<br><br>
<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
<div dir="rtl" style="text-align: justify">
        <b>
        لمزيد من المعلومات:
        </b>
</div>

* [Why does regularizing the bias lead to underfitting in neural networks?](https://www.deepwizai.com/simply-deep/why-does-regularizing-the-bias-lead-to-underfitting-in-neural-networks)
* [Why We Need Bias in Neural Networks](https://towardsdatascience.com/why-we-need-bias-in-neural-networks-db8f7e07cb98)

<br>

<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">