---
title: "؟Vanishing gradientsكيف نتعامل مع مشكلة الـ"
subtitle: "ما أسبابها وما حلولها؟"
#description: <descriptive text here>
date: 2021-12-14T16:46:33+02:00
draft: false
toc: false
image: ""
featuredImage: ""
featuredImagePreview: "lib/img/Sigmoid function saturation regions.jpg"
tags: [Machine Learning, Deep Learning, Vanishing Gradient, Neural Networks, Batch Normalization, Keras, Leaky ReLU, ELU, Sigmoid, Tanh]
categories: [مما تعلمت]
---

<div dir="rtl" style="text-align: justify">
    <h4>
        الـVanishing Gradients هي أحد اكبر المشاكل الى أثرت على تطوّر الـneural networks لفترة طويلة، وبسببها كان تدريب الـdeep neural networks عملية صعبة وبتستغرق وقت كبير، لكن إيه السبب وراء المشكلة دى؟
    </h4>
</div>

<!--more-->

<p align="center">
    <img src="/lib/img/vanishing gradients problem.png" alt="vanishing gradients problem"/>
    <br>
    Fig 1: Vanishing gradients.
    <br> 
    Source: <a href="https://codeodysseys.com/posts/gradient-calculation/">Click here</a> 
</p>

<div dir="rtl" style="text-align: justify">
    <h4>
        أحد أسباب المشكلة هو الـrandom weights initialization عن طريق normal distribution الـmean بتاعه صفر والـvariance واحد، استخدام الطريقة دى مع activation function زى sigmoid أو tanh كان يؤدي إلى إن الـoutput variance في كل layer يكون أكبر من الـinput variance، ده كان بيسبّب إن الـsigmoid في الـlayers الآخيرة توصل للـsaturation (لاحظ Figure 2)، فيصبح الـgradient صغير جداً أو بصفر، بالتالي في الـbackpropagation مش هقدر أعمل updates والـlearning هياخد وقت كبير وممكن يتوقف تماماً.
    </h4>
    <p align="center">
        <img src="/lib/img/Sigmoid function saturation regions.jpg" alt="Sigmoid function saturation regions"/>
        <br>
        Fig 2: Sigmoid function saturation regions.
    </p>
    <h4>
        في Paper لـXavier Glorot، قال إن عشان نساوي الـinput variance والـoutput variance لازم يكون عدد الـinputs والـoutputs متساوي، لكنه قدم حل آخر يعوّض الشرط ده وهو الـGlorot initialization.
        <br>
        (<bdo dir="ltr">$fan_{out}$, $fan_{in}$</bdo> هو عدد الـinput neurons والـoutput neurons على الترتيب):
    </h4>
</div>


{{< admonition type=tip title="Glorot Initialization" open=True >}}
$\text{Where } fan_{avg} = (fan_{in} + fan_{out}/2), \text{ initialize weights with:}$ 

* $\text{Normal Distribution with mean 0 and variance } \sigma^2 = \frac{1}{fan_{avg}}$

* $\text{Or a uniform distribution between -r and +r with } r = \sqrt{\frac{3}{fan_{avg}}}$
{{< /admonition >}}

<div dir="rtl" style="text-align: justify">
    <h3>
        طيب المشكلة دى ظهرت بسبب الـsaturation في sigmoid و tanh، هل ممكن احلها لو استخدمت ReLU؟
    </h3>
    <h4>
        الـReLU بتحل المشكلة دى في الجزء الموجب، لكنها بتقدم مشكلة جديدة بسبب الجزء السالب; لإن لو كان الـinput بتاعها سالب هيكون الـoutput صفر، وكمان الـgradient صفر، فـ مش هيحصل update للـweights، وبالتالي في الـstep الجاية هيظل الـouput والـgradient صفر، هنا يقال إن الـneurons are dead: لإن مش هيحصل updates عندهم على الإطلاق.
    </h4>
    <h4>
        الحل المباشر للمشكلة هو إننا نضيف قيمة للـfunction في الجزء السالب، فممكن نلجأ للـLeaky ReLU (موضحة في Figure 3); وفيها بنضيف ميل صغير جداً للـReLU في الجزء السالب، الميل ده إما يكون hyper parameter أنا الى بحدده، أو يكون رقم عشوائي في كل step (تُسمّى Randomized Leaky ReLU) وده بيضيف نوع من الـregularization على الـnetwork، أو يكون parameter بتتعلمه الـnetwork (تُسمّى Parametric Leaky ReLU) وده بيدي الـNetwork حرية أكبر، لكن ممكن يسبب overfitting لو الـdataset مش كبيرة بشكل كافي.
    </h4>
    <p align="center">
        <img src="/lib/img/leaky ReLU.jpg" alt="leaky ReLU"/>
        <br>
        Fig 3: Leaky ReLU activation function.
    </p>
    <h4>
        وممكن نلجأ للـELU (موضّحة في Figure 4): وفيها الجزء السالب من الـReLU بياخد شكل الـexponential function، ورغم إنها ابطأ من الـReLU في الحساب، لكنها بتوصل للـconvergence في عدد خطوات أقل، لذلك في الـtraining بشكل عام بتكون اسرع، لكن في الـtesting فالـReLU بالتأكيد أسرع.
    </h4>
    <p align="center">
        <img src="/lib/img/ELU.jpg" alt="leaky ReLU"/>
        <br>
        Fig 4: ELU activation function.
    </p>
    <hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
    <h4>
        حتى الآن تعرضنا لبعض حلول الـVanishing gradients عن طريق الـGlorot initialization والـNon saturating activations زى الـReLU ومشتقاتها، لكن الحلول دى بتقيّد تصميم الـnetwork بالشروط الى بحطها على اختيار الـactivation وكمان بتعتمد بشكل كبير على الـinitialization.
    </h4>
    <h3>
        هل ممكن نتفادى القيود دى؟
    </h3>
    <h4>
        أحد الأسباب الى ممكن تؤدى إلى unstable training هو الـInternal Covariate shift: ومعناه إن الـinput distribution الخاص بـlayer معيّنة بيتغير أثناء الـtraining بسبب الـweight update في الـlayers السابقة، الـshift المستمر ده بيأثر على كفاءة الـtraining لإن الـweight update التالي هيحاول يلافي تأثير التغيير في الـweights في الـlayers الى قبله عشان يقدر يوصل للـminimum cost.
    </h4>
    <p align="center">
        <img src="/lib/img/internal covariate shift.png" alt="Internal covariate shift"/>
        <br>
        Fig 5: Covariate shift vs. internal covariate shift.
        <br> 
        Source: <a href="https://www.diva-portal.org/smash/get/diva2:955562/FULLTEXT01.pdf">Click here</a> 
    </p>
    <h4>
        الحل هنا هو إننا نحاول نثبت الـinput distribution بأكبر شكل ممكن، وده بيتم عن طريق الـBatch normalization: وفيها بنعمل standardization لناتج الـneurons قبل أو بعد الـactivation function بحيث يكون الـdistribution دايماً له نفس الـmean والـvariance بغض النظر عن الـbatch الحالية أو الـweights (لاحظ Figure 6).
    </h4>
    <p align="center">
        <img src="/lib/img/batch norm.jpg" alt="Batch normalization"/>
        <br>
        Fig 6: Batch normalization layer. Varying data distributions across batches are normalized.
        <br> 
        Source: <a href="https://towardsdatascience.com/understanding-dataset-shift-f2a5a262a766">Click here</a> 
    </p>
    <h4>
        وبجانب الـstandardization كمان بنضيف scale & shift parameters بتتعلمهم الـnetwork عشان تقدر تحدد شكل الـdistribution المناسب لها في الـtraining، وبالتالي ممكن تلغي الـbatch norm بنفسها لو مش هتحتاجه.
    </h4>
</div>


{{< admonition type=tip title="How to perform batch normalization" open=True >}}
$\text{Input: Values of } x \text{ over a mini-batch: } B =  \\{x_{1...m}\\}; \text{  Learned parameters: } \gamma,\beta$

$\text{Output: } \\{ y_i =BN_{\gamma,\beta}(x_i) \\}$

$$
\mu_{B} = \frac{1}{m}\sum_{i=1}^{m} x_i \hspace{17mm} \Rightarrow \text{mini-batch mean} \newline 
\rule{0mm}{7mm}
\sigma_B^2 = \frac{1}{m}\sum_{i=1}^{m} (x_i-\mu_{B})^2 \hspace{1cm} \Rightarrow \text{mini-batch variance} \newline 
\rule{0mm}{7mm}
\hat{x}_i = \frac{x_i-\mu_B}{\sqrt{\sigma_B^2 + \epsilon}}\hspace{16mm} \Rightarrow \text{normalize input} \newline
\rule{0mm}{5mm}
y_i = \gamma \hat{x}_i + \beta \hspace{17mm} \Rightarrow \text{scale and shift} \newline 
$$
{{< /admonition >}}

<div dir="rtl" style="text-align: justify">
    <h4>
        ورغم إن فيه خلاف حول موضوع الـinternal covariate shift وتأثيره على الـtraining، ده لا ينفي إن الـBatch normalization طريقة فعالة جداً وبتحسن عملية الـtraining بشكل كبير: دلوقتي الـnetwork أصبحت غير معتمدة بشكل كبير على الـweight initialization زى الأول، وكمان الـdistribution أصبح ثابت تقريباً في كل layer وقللت احتمالية وصول الـinput للـsaturation regions، وبالتالي أقدر أستخدم learning rate أكبر وأسرّع الـtraining أكتر، كمان لو استخدمت الـbatch norm قبل الـactivation function فممكن أستغنى عن الـbias، لإن الـshift parameter الموجود في الـbatch norm يؤدي نفس الوظيفة.
        <br>
        وفوق كل ده كمان فالـbatch norm بيضيف نوع من الـregularization على الـnetwork، لإنه بيحسب الـmean والـvariance في كل batch على حدة، وده بيضيف بعض الـnoise على الداتا لإنهم في الغالب مختلفين عن الـmean والـvariance الخاص بتوزيع الداتا بالكامل.
    </h4>
    <h3>
        أثناء الـtraining بقدر احسب الـmean والـvariance على كل batch،ايه الى بيحصل في الـtesting؟
    </h3>
    <h4>
        أثناء الـtraining ممكن نحسب moving average للـmeans والـvariances الخاصة بكل batch عشان تقرّب من الـmean والـvariance الخاص بالـtraining data، وهم دول الى بستخدمهم في الـbatch norm بعد الـtraining، ومع إن الـbatch norm بيأثر على سرعة الـtesting بسبب الحسابات الإضافية، لكن مزاياه بتعّوض التأثير ده، بالإضافة إلى إننا بعد الـtraining ممكن ندمج الـbatch norm parameters مع الـweights الخاصة بالـlayer الى قبله، وبالتالي نقلل الحسابات ونسرّع زمن الـtesting
    </h4>
</div>

{{< admonition type=tip title="How to fuse BN layer with the previous layer" open=True >}}
If the previos layer computes $XW+b$ then BN layer will compute $\gamma \otimes (XW+b-\mu)/\sigma + \beta$ (ignoring the smoothing term $\epsilon$ in the denominator).

If we define $W'=\gamma \otimes W/\sigma$ and $b'=\gamma \otimes (b-\mu)/\sigma+\beta$, the equation simplifies to $XW'+b'$. So if we replace the previous layer's weights and biases ($W$ and $b$) with the updated weights and biases ($W'$ and $b'$), we can get rid of the BN layer.
{{< /admonition >}}

<br>
<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">

<div dir="rtl" style="text-align: justify">
    <b>
    لمزيد من المعلومات:
    </b>
</div>

* [Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow](https://www.oreilly.com/library/view/hands-on-machine-learning/9781492032632/)
* [Understanding the difficulty of training deep feedforward neural networks, Xavier Glorot, Yoshua Bengio ; PMLR 9:249–256](http://proceedings.mlr.press/v9/glorot10a/glorot10a.pdf)
* [How Does Batch Normalization Help Optimization?](https://arxiv.org/pdf/1805.11604.pdf)
* [Batch Normalization: An Incredibly Versatile Deep Learning Tool](https://towardsdatascience.com/batch-normalization-the-greatest-breakthrough-in-deep-learning-77e64909d81d)
* [BatchNormalization- a technique that enhances training](https://medium.com/geekculture/batchnormalization-a-technique-that-enhances-training-5d44966c22c0)
* [Implementing Batch Normalization in Python](https://towardsdatascience.com/implementing-batch-normalization-in-python-a044b0369567)
* [Understanding Dataset Shift](https://towardsdatascience.com/understanding-dataset-shift-f2a5a262a766)

<br>

<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">