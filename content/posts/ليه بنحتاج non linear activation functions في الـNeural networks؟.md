---
title: "؟Neural networksفي الـ Non-linear activation functions ليه بنحتاج"
subtitle: "وايه الى هيحصل من غيرها؟"
date: 2021-11-09T13:08:51+02:00
toc: false
draft: false
featuredImage: ""
featuredImagePreview: "/lib/img/non-linear-activation-functions.png"
tags: [Machine Learning, Deep Learning, Neural Networks]
categories: [مما تعلمت]
---

 
<div dir="rtl" style="text-align: justify">
    <h4>
        الهدف النهائي الى أنا عايزه من الـneural network هو انها تعبر بشكل تقريبي عن العلاقة ما بين $input \hspace{1mm} (X)$  و $target$ او $output \hspace{1mm} (y)$، بمعنى آخر أنا عايزها تـapproximate دالة $f$ حيث $f(x)=y$.
    </h4>
</div>

<!--more-->
<br>

<div dir="auto" style="text-align: justify">
    <h3> 
        هل الـneural networks تقدر تحقق العلاقة دى؟
    </h3>
    نظرية الـuniversal approximation بتقول إن اي neural network فيها hidden     layer واحدة فقط فيها عدد معيّن من الـneurons، تقدر تعبر عن أي continuous function انت عايزها ولكن لازم تاخد في الاعتبار بعض الشروط، أحد الشروط دى هى الـnon linear activation function.
    <br><br>
    <h3> 
        طيب ليه مش هقدر أحقق الـuniversal approximation من غير الـnon linear activation function؟
    </h2>
    لإن من غيرها فكل الحسابات الى بتحصل داخل الـneural network ناتجة عن عمليات linear، كلها عمليات ضرب وجمع، لذلك المحصلة النهائية من كل ده هي إن الـneural network مش هتقدر تعبر عن أي حاجة اكتر تعقيداً من linear relationship.
    <br><br>
    <h3> 
        طيب هل لو زودت أكتر من hidden layer واحدة هقدر أوصل لـnon linear relationship؟
    </h3>
    إضافة hidden layer تانية كتير لا يعني إن انا بضيف non linearity في العلاقة، في الحقيقة كل الـhidden layers دى بتعمل linear transormations متتابعة مش أكتر، والمفاجأة إن كل الـhidden layers الى أنا ضفتها ممكن أستعوض عنهم بـhidden layer واحدة بس تؤدي كل الـlinear transformations دى مرة واحدة، والمفاجأة التانية هى إن كل الـneurons الموجودة في الـhidden layer الواحدة دى ممكن استعوض عنهم بـneuron واحد بس.
    <br><br>
    <h4> 
    تفتكر كان إيه اسم الـmodel المكوّن من neuron واحد فقط ومفيش فيه non linear activation function؟ أيوة هو الـlinear regression.
    </h4>
    فبالتالي أي neural network لا تستخدم activation function قدرتها لا تتعدى قدرة اي linear regression model بسيط.
</div>
<br><br>
<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
<div dir="rtl" style="text-align: justify">
        <b>
        لمزيد من المعلومات:
        </b>
</div>


* [You Don’t Understand Neural Networks Until You Understand the Universal Approximation Theorem](https://medium.com/analytics-vidhya/you-dont-understand-neural-networks-until-you-understand-the-universal-approximation-theorem-85b3e7677126)
* [Neural Networks and the Universal Approximation Theorem](https://towardsdatascience.com/neural-networks-and-the-universal-approximation-theorem-8a389a33d30a)
* [Everything you need to know about “Activation Functions” in Deep learning models](https://towardsdatascience.com/everything-you-need-to-know-about-activation-functions-in-deep-learning-models-84ba9f82c253)

<br>

<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">