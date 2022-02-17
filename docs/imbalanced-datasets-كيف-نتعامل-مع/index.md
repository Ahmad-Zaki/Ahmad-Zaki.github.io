# ؟Imbalanced Datasets كيف نتعامل مع


<div dir="rtl">
    <h4>
        من المشاكل الى قابلتها في الكورسات إن الـdataset أحياناً بتكون خالية من العيوب الى ممكن نلاقيها في أرض الواقع، وفي مجال الـclassification، فيه فرصة كبيرة إننا نتعامل مع imbalanced datasets لإن الأكيد إن مش كل الـclasses بتحدث بنفس النسبة وبالتالي الغير طبيعي هو إن لما أجمع data أشوف كل الـclasses بنفس الrate ، لكن ليه الـimbalance ده ممكن يسبب مشكلة أصلا؟
    </h4>
</div>
<!--more-->

<!--
helpful html tags:
<div dir="rtl"></div>

<br>

<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">

-->

<p align="center">
    <img src="/lib/img/Imbalanced Dataset.jpg" alt="Imbalanced Dataset"/>
    <br>
    Fig 1: Example of an imbalanced dataset 
</p>

<div dir="rtl">
        ممكن نطرح المشكلة بمثال: لنفترض إن عندي dataset فيها نتائج فحص عن ورم معين في الجسم، فيها 10 ألاف ريكورد عن بيانات مرضى مختلفين والمطلوب مني إن أنا ابني model يتنبأ بوجود الورم ده بناء على بيانات المريض، فأنا دربت موديل بسيط وصلت دقته 99%.
        <br>
        <h3>
                نتيجة جيدة بالنسبة لأول تجربة صح؟
        </h3>
        في البداية ممكن تتفق، لكن خلينا نلقي نظرة على الdata الأول: لما بصيت على الداتا لقيت إن فيه 100 ريكورد فقط من بين الـ10 آلاف عندهم الورم، والباقي نتايجهم سلبية ، بمعنى آخر: 99% من المرضى الموجودين في الداتا مفيش عندهم ورم.
        <br><br>
        تخيل معايا بقى إن أنا عملت موديل بيفترض إن كل المرضى مفيش عندهم ورم، يعني بيتنبأ إن كل العينات سلبية، الموديل الساذج ده قدر يوصل لـ99% accuracy من غير ما يعمل حاجة! يبقى هنا الـaccuracy أصبحت معيار غير صحيح لنتايج الموديل بتاعي.
        <br>
        <h3>
                ليه مقدرش أعتمد على الـaccruacy في مشكلة زى دى؟
        </h3>
</div>
<!-- $$
Accuracy = \frac{True \hspace{1mm} Positive + True \hspace{1mm} Negative}{All \hspace{1mm} Data}
$$ -->
$$
Accuracy = \frac{TP + TN}{TP + FP + TN + FN}
$$
$$
TP: True Postive \hspace{1mm} | \hspace{1mm} FP: False Positive \hspace{1mm} | \hspace{1mm} TN: True Negative \hspace{1mm} | \hspace{1mm} FN: False Negative
$$
<div dir="rtl">
        لإن الـaccuracy بتهتم بحساب نسبة الـTrue positive والـTrue negative، في الحالة الى عندي هنا الـNegative يمثّل 99% من الـdata، وبالتالي حتى لما ضحيت بجزء الـtrue positive لم أفقد أي جزء يذكر من الـaccuracy، فكان ده السبب إن الـaccuracy كانت عالية جداً بالنسبة لـmodel حرفياً متعلمش أي حاجة.
        <br>
        <h3>
                طب إيه الحل؟ إزاي أقدر احكم على مدى صحة توقعات الموديل في الحالة دى؟
        </h3>
        في المشكلة الى عندي، تكلفة إن المريض يبقى عنده ورم وأنا اتنبأ إن معندوش كبيرة جداً: الورم ممكن يكلفه حياته، فبالتالي لازم أختار measure بيتأثر تحديداً بنسبة الـFalse negative وهو الـrecall، لو حسبت الـrecall للموديل البسيط الى عندي هيساوي 0% وهنا تكون النتيجة منطقية.
</div>
$$
Recall = \frac{TP}{TP + FN}
$$
<div dir="rtl">
        في حالة أخرى ممكن تكون تكلفة الـFalse positive كبيرة وعندها لازم أختار measure بيتأثر بيه وهو الـPrecision:
</div>
$$
Precision = \frac{TP}{TP + FP}
$$
<div dir="rtl">
        ولو محتاج أتابع الأتنين مع بعض يبقى أختار الـF1 score وهو أفضل معيار ممكن استخدمه في حالة التعامل مع Imbalanced data، لإنه بيتأثر بنسبة الـFalse positive والـFalse negative في نتائج الموديل.  
</div>
$$
F_{1} = 2 \times \frac{Precision \times Recall}{Precision + Recall}
$$
<br>
<div dir="rtl">
        <hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
        <h4>
        بعد ما عرفت الـmeasure الى أقدر احكم بيه على نتايج الموديل، الخطوة التالية هي إننا نعمل موديل بيتعلم بكفاءة من الـimbalanced data وبيأدي بشكل جيد سواء على الـmajority class (الى بتمثّل غالبيّة الـdataset) أو الـminority class، 
        </h4>
        <h3>
                طب ليه الـimbalance ممكن يسببلي مشكلة هنا؟
        </h3>  
        في مرحلة الtraining، الموديل بيهدف إنه يوصل للـparameters الى الـcost عندها أقل ما يمكن، خلال المرحلة دى (ولإن الـmajority class تمثّل غالبية الصفوف في الداتا)، هتلاقي إن معظم الـupdates رايحة ناحية تحسين نتائج الموديل على الـmajority class ، لإن فرصة الـmissclassification فيها أعلى بكتير، وده معناه إنها بتساهم في الـcost بشكل اكبر بكتير من الـminority class، فبالتالي هينتج موديل عنده bias ناحية الـmajority class وبيأدي فيها بشكل أفضل بكتير من الـminority class. 
        <p align="center">
                <img src="/lib/img/Imbalanced Dataset Confusion Matrix.jpg" alt="Imbalanced Dataset Confusion Matrix"/>
                <br>
                <p dir="rtl" align="center">Fig 2: مثال على نتائج model بعد تدريبه على imbalanced dataset،<br> لاحظ نسبة الخطأ الكبيرة في الـminority class</b>
                <br> 
                Source: <a href="https://www.youtube.com/watch?v=vJkWB_kmadQ">Click here</a>
        </p>
        <br>
        <h3>
                أحد الطرق الى ممكن تحل مشكلة الـclass imbalance هي إننا نخلق balance بنفسنا عن طريق الـdata resampling وده ممكن يتم بأكتر من طريقة:
        </h3>  
        الطريقة الأولى هي الـRandom Under-sampling: عندك class مسيطر عالداتا ؟ بسيطة: احذف صفوف منه عشوائياً لحد ما يتساوى بالـclass التاني.
        <br><br>
        حل بسيط لكنه غير منطقي، الناس بتحاول تجمع أكبر قدر ممكن من الداتا، وانت رايح تحذفها؟! وهو فعلاً مش أفضل حل لإن حتى لو حلّيت مشكلة الـimbalance هتدخل في مشكلة تانية لإنك فقدت جزء كبير من الداتا ،وكمان ممكن الـsample الصغيرة الى حافظت عليها تكون biased ومش بتعبر عن الـmajority بشكل كويس ،وبالتالي مش هتلاقي نتيجة جيدة.
        <p align="center">
                <img src="/lib/img/Undersampling Vs. Oversampling.jpg" alt="Undersampling Vs. Oversampling"/>
                <br>
                Fig 3: Random Undersampling Vs. Random Oversampling 
        </p>
        <br>
        الطريقة التانية هي الـRandom Over-sampling: بدل ما نحذف من الـmajority ، ممكن نكرر صفوف من الـminority بشكل عشوائي لحد ما تتساوى بالـmajority، وكده أكون تفاديت أي loss في الـdata وقدرت احل مشكلة الـimbalance.
        <br><br>
        لكن تظل عندي مشكلة تانية وهي إن الصفوف الى زودتها هى تكرار للصفوف الموجودة عندي في الداتا، وده معناه إنها redundant، ولذلك ممكن يحصل overfitting للموديل وميقدرش يوصل لـgeneralization جيد.
        <br><br><br>
        الطريقة التالتة هي الـSynthetic Minority Over-sampling: وهى بتحاول تحل مشكلة الـover-sampling عن طريق إنها تخلق صفوف جديدة من الـminority من الصفوف الموجودة فعلاً في الـdata. كده ضمنت حل الـimbalance وضفت data جديدة وحليت مشكلة الـredundant data.
        <p align="center">
                <img src="/lib/img/SMOTE.jpg" alt="SMOTE"/>
                <br>
                Fig 4: Synthetic Minority Over-sampling
        </p>
        <br>
        <h3>
                طيب إزاي بنعمل data points جديدة من الداتا الأصلية؟
        </h3>  
        بتبدأ الأول بنقطة عشوائية من الـminority، وتحدد أقرب 5 نقط minority لها، بعد كده بتختار واحدة منهم وترسم خط بين النقطتين، النقطة الجديدة الى هضيفها هتكون أي نقطة عشوائية على الخط بين النقطتين دول. ممكن تتخيل الطريقة دى كإنك بتعمل interpolation بين النقط وتختار نقط عشوائياً على الخطوط دى.
        <h3>
                إيه أفضل طريقة من الـ3 طرق دول؟
        </h3>  
        اختيار الطريقة معتمد بشكل رئيسي على نوع الداتا، مفيش طريقة مضمون نجاحها بشكل كامل وممكن تحتاج تستخدم أكثر من طريقة مع بعض، وفي النهاية نتائج الموديل هي المعيار.
        <hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
        <h4>
        حتى الآن تطرقنا لأكثر من طريقة لحل مشكلة الـimbalance عن طريق الـdata resampling، لكن فيه methods تانية لتقليل تأثير الـimbalance عن طريق الـmodels، هذكر أمثلة منها الآن
        </h4>
        <h3>
                الطريقة الأولى هي الـCost-Sensitive training:
        </h3>  
        زى ما وضحنا سابقاً  إن في مرحلة الـtraining بنلاقي إن الـparameters update دايماً بتكون في اتجاه تحسين نتايج الموديل على الـmajority class لإنها بتساهم بشكل أكبر في الـcost، طب إيه رأيك نرفع الـpenalty الخاصة بالـminority؟
        <br>
        يعني إيه؟ ببساطة بدل ما أفترض إن كل الـclasses لهم نفس الوزن والتأثير على الـcost في الـtraining، هبدأ احط وزن أكبر للـminority class واحط وزن أقل للـmajority class.
        <br>
        كده في الـtraining لما يحصل error في الـminority هيكون له تأثير كبير على الـcost، وبالتالي قدرت أساوي تأثير الـmajority والـminority وقللت bias الموديل ناحية الـmajority
        <p align="center">
                <img src="/lib/img/Cost Sensitive Training.jpg" alt="Cost Sensitive Training"/>
                <br>
                Fig 5: Effect of Cost Sensitive Training on model results.
                <br> 
                Source: <a href="https://www.youtube.com/watch?v=vJkWB_kmadQ">Click here</a>
        </p>
        <br>
        <h3>
                الطريقة التانية هى إننا نستخدم ensemble algorithm:
        </h3> 
        في الغالب أي ML model هنستخدمه لوحده مش هيأدي بشكل جيد في حالة الـimbalanced data، لذلك ممكن نلجأ لعمل ensemble من أكتر من model سواء عن طريق الـboosting (زى الـGradient boosted trees) أو الـbagging (زى الـRandom forest).
        <br><br>
        الـensembles بتحقق نتايج أحسن بشكل عام، لكن ممكن نستغل الـbagging أكتر عن طريق إننا نحاكي الـunder-sampling داخل الـensembled models كالتالي:
        <p align="center">
                <img src="/lib/img/model ensembles with data resampling.jpg" alt="model ensembles with data resampling"/>
                <br>
                Fig 6: Model ensemble with data resampling. 
                <br>
                Source: <a href="https://www.kdnuggets.com/2017/06/7-techniques-handle-imbalanced-data.html">Click here</a>
        </p>
        <br>
        في الـbagging بنقسم الـdataset على كل موديل with replacement، ممكن نقسم احنا الداتا بحيث يكون جزء الـminority بالكامل موجود في كل موديل ونعمل sampling للـmajority ونوزعهم على كل موديل بالتساوي.
        <br>
        وكده يبقى كل موديل عنده عدد متساوي من الـmajority class والـminority class وفي نفس الوقت استخدمت كل الـdata ومخسرتش منها حاجة في الـunder-sampling.
</div>


<br><br>
<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
<div dir="rtl">
        <b>
        لمزيد من المعلومات:
        </b>
</div>

* [Machine Learning with Imbalanced Data - Part 2 (Cost-sensitive Learning)](https://www.youtube.com/watch?v=vJkWB_kmadQ)
* [7 Techniques to Handle Imbalanced Data](https://www.kdnuggets.com/2017/06/7-techniques-handle-imbalanced-data.html)
* [Dealing with Imbalanced Data](https://towardsdatascience.com/methods-for-dealing-with-imbalanced-data-5b761be45a18)

<br>

<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
