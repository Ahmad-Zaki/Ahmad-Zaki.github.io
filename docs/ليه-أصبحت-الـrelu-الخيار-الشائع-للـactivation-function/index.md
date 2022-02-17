# ؟Activation functionsالخيار الشائع للـ ReLUليه أصبحت الـ


<div dir="rtl">
    <h4>
        ReLU هو اختصار Rectified Linear Unit وهي واحدة من أكثر الـactivation functions المستخدمة في الـNeural networks، رغم إن اسمها يحتوى على كلمة linear لكنها non linear function، وده واضح رياضياً وحتى من الرسم بتاعها، لكن ليه بتقدر تنافس الـactivation functions الأخرى المكوّنة من smooth curves رغم إنها حرفياً عبارة عن خطين بس؟
    </h4>
</div>

<!--more-->

<!--
helpful html tags:
<div dir="rtl"></div>

<br>

<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">

-->

$$
ReLU=max(0,x)
$$

<p align="center">
    <img src="/lib/img/ReLU.JPG" alt="ReLU function Graph"/>
    <br>
    Fig 1: ReLU Graph
</p>



<div dir="rtl">
    ميزة الـReLU الكبرى مش بتظهر لما أستخدم واحدة بس منها، لكن بتكون واضحة لما أجمع شوية دوال ReLU كتير مع بعض، نقطة الانكسار الموجودة بين جزئين دالة الـReLU بتقدر تقرّبلك مجموعة الخطوط الكتيرة دى من أي منحنى أنت عايزه عن طريق إنها بتتيح لك تكسر الخط المستقيم في أي مكان انت عايزه (أو المكان الى الـneural network بتتعلمه).  
    <br><br>
    لاحظ الصورة التالية: لما استخدمت ReLU واحدة بس مقدمتش اختلاف كبير عن الـstraight line، لكن لما بدأت اعمل nesting واجمعهم مع بعض بدأت أكوّن شكل جديد مقدرتش أوصله لما جمعت الـstraight lines مع بعض.
    <p align="center">
        <img src="/lib/img/ReLU_vs_linear.jpg" alt="ReLU vs. linear function"/>
        <br>
        Fig 2: ReLU vs. Linear function
    </p>
    <br>
    <h3>
        طيب هل هي فعلاً تقدر توصل لمنحنيات صعبة زى باقي الـactivation function؟
    </h3>
    الإجابة القصيرة: نعم، وده واضح من خلال استخدامها الشائع والواسع في كل مجالات الـdeep learning، لاحظ الصورة التالية: عن طريق استخدام أكتر من ReLU function قدرت أوصل لشكل تقريبي من منحنى <bdo dir="ltr">$x^3+x^2-x-1$</bdo>
    <p align="center">
        <img src="/lib/img/ReLU_Approximation.JPG" alt="ReLU_Approximation"/>
        <br>
        Fig 3: We can use many ReLUs to approximate complex functions <br>
        Source: <a href="https://towardsdatascience.com/can-neural-networks-really-learn-any-function-65e106617fc6">Click here</a>
    </p>
    <br>
    وبالنسبة للـNeural networks، في الصورة التالية نقدر نلاحظ إن neural network بتستخدم ReLU كـActivation function وصلت لمنحنى قريب من المنحنى التانج عن neural network بتستخد $tanh$ في نفس عدد الـepoch
    <p align="center">
        <img src="/lib/img/ReLU_vs_TANH_in_neural_networks.jpg" alt="ReLU vs tanh"/>
        <br>
        <p dir="rtl" align="center">Fig 4: كل network تحتوي 3 hidden layers، كل منهم يحتوى 3 neurons</b><br>
        Source: <a href="https://towardsdatascience.com/if-rectified-linear-units-are-linear-how-do-they-add-nonlinearity-40247d3e4792">Click here</a>
    </p>
    <h3>
        طيب ما الـtanh وصلت لـsmooth curve وعملت نتيجة كويسة بردو، ليه اروح للـReLU؟
    </h3>  
    هنا تيجي ميزة الـReLU التانية والى بتديلها أفضلية كبيرة عن الـtanh: إنها very computationally efficient، هي في الآخر عبارة عن خط وتفاضلها ثابت، على عكس الـtanh الى فيها exponentials وتفاضلها متغير.  
</div>

<br><br>
<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
<div dir="rtl">
        <b>
        لمزيد من المعلومات
        </b>
</div>

* [Finally, an intuitive explanation of why ReLU works](https://towardsdatascience.com/if-rectified-linear-units-are-linear-how-do-they-add-nonlinearity-40247d3e4792)
* [Can neural networks solve any problem?](https://towardsdatascience.com/can-neural-networks-really-learn-any-function-65e106617fc6)


<br>

<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
