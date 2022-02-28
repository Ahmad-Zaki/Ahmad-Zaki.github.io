# ؟ConvNetsفي الـ Backpropagationكيف يتم تنفيذ الـ


<div dir="rtl" style="text-align: justify">
    <h4>
        في الغالب كانت بداية معرفتك بالـbackbropagation عن طريق شرحه على Fully connected neural network, لكن بعد ما تتطرق لأشكال أخرى من الـneural netowrks مثل الـconvolutional neural network بتلاقي إن الـbackpropagation مستخدم فيهم كـalgorithm رئيسي لحساب الـgradients في كل layer لكن بدون ذكر تفاصيل أكثر عنها، لإنك بالفعل غير مضطر لمعرفة تفاصيل الرياضية قبل استخدامها، ومع ذلك فمعرفة كيف تتم داخل الـConv layers  قد يكون سؤال مر ببالك ولو من باب الفضول في وقت من الأوقات.
    </h4>
</div>

<!--more-->



<div dir="rtl" style="text-align: justify">
{{< admonition type=note title="Note" open=true >}}
جميع الصور والـAnimation مصدرها هو [المقال الرائع](https://pavisj.medium.com/convolutions-and-backpropagations-46026a8f5d2c) ده والى بيتكلم عن الـbackpropagation في الـconvnets بشكل مبسط وجيد جداً، أنصح بقراءته لمزيد من التفاصيل.
{{< /admonition >}}
{{< admonition type=tip title="Tip" open=true >}}
يفضّل أن يكون لديك معرفة كافية بخطوات الـbackpropagation واستخدام الـderivatives والـchain rule فيه قبل قراءة المقال.
{{< /admonition >}}
    <h3>
        إذاً كيف يتم تنفيذ الـBackpropagation في الـConvNets؟
    </h3>
    <h4>
        لنفترض إن عندنا Input <bdo dir="ltr">$X_{3×3}$ </bdo> و Filter <bdo dir="ltr">$F_{2×2}$</bdo> كالتالي:
        <bdo dir="ltr">
            $$X_{3×3} = \left( \begin{array}{ccc}
            X_{11}&X_{12}&X_{13} \\
            X_{21}&X_{22}&X_{23} \\
            X_{31}&X_{32}&X_{33} \\
            \end{array} \right) \hspace{10mm}
            F_{2×2} = \left( \begin{array}{cc}
            F_{11}&F_{12} \\
            F_{21}&F_{22} \\
            \end{array} \right)
            $$ 
        </bdo>
        في الـforward pass نحصل على ناتج الـconvolution بين $X$ و $F$ (على اعتبار ان الـstride=1 ,والـpadding=0) وهو <bdo dir="ltr">$O_{2×2}$ </bdo>،و تتم العملية كالتالي:
        <bdo dir="ltr">
            $$\underbrace{\left( \begin{array}{cc}
            O_{11}&O_{12} \\
            O_{21}&O_{22} \\
            \end{array} \right)}_{O_{2×2}} = 
            \underbrace{\left( \begin{array}{ccc}
            X_{11}&X_{12}&X_{13} \\
            X_{21}&X_{22}&X_{23} \\
            X_{31}&X_{32}&X_{33} \\
            \end{array} \right)}_{X_{3×3}} \otimes
            \underbrace{\left( \begin{array}{cc}
            F_{11}&F_{12} \\
            F_{21}&F_{22} \\
            \end{array} \right)}_{F_{2×2}}
            $$ 
        </bdo>
    </h4>
    <p align="center">
        <img src="/lib/img/cnn forward pass.gif" alt="forward pass in a conv layer"/>
        <br>
        Fig 1: Forward pass in a convolution layer with one filter
    </p>
    <h4>
        عملية الـBackpropagation (موضّحة في Fig 2) تتم عن طريق الـChain rule، بنبدأ من آخر layer ونحسب الـloss gradient <bdo dir="ltr">$\frac{\partial L}{\partial z}$</bdo> بتاعها ونتحرك للـlayer الى قبلها. في الlayer التالية بنحسب الـloss gradient عندها عن طريق ضرب <bdo dir="ltr">$\frac{\partial L}{\partial z}$</bdo> في الـlocal gradients الخاصة بالـlayer وهي كل من <bdo dir="ltr">$\frac{\partial z}{\partial x}$</bdo> و <bdo dir="ltr">$\frac{\partial z}{\partial y}$</bdo>.
    </h4>
    <p align="center">
        <img src="/lib/img/neuron backpropagation.png" alt="backpropagation"/>
        <br>
        Fig 2: Gradients flow in a neuron
    </p>
    <h4>
        في الـfully connected neural network، حساب الـlocal gradients يتم بطريقة مباشرة لأن z عبارة عن دالة في كل من x,y، لكن في الـconvolutional neural network نجد أن O عبارة عن ناتج convolution بين الـweights (الـfilter parameters) والfeature map X.
    </h4>
    <p align="center">
        <img src="/lib/img/back prop cnn.JPG" alt="backpropagation in CNN"/>
        <br>
        Fig 3: Gradients flow in a convolutional neuron
    </p>
    <h3>
        ليه بنحتاج نحسب الـlocal gradients في الـconvolution؟
    </h3>
    <h4>
        <bdo dir="ltr">$\frac{\partial O}{\partial F}$</bdo> بنستخدمه عشان نحسب الـloss gradient بالنسبة لـF، وهو ده الى بنستخدمه عشان نعمل update لقيم الـfilter:
        <bdo dir="ltr">
            $$
            \frac{\partial L}{\partial F} = \frac{\partial O}{\partial F} × \frac{\partial L}{\partial O} \\ \rule{0mm}{7mm}
            F_{updated} = F - \alpha \frac{\partial L}{\partial F}
            $$ 
        </bdo>
        أما بالنسبة لـ <bdo dir="ltr">$\frac{\partial O}{\partial X}$</bdo>، بما أن X هى output الـlayer السابقة، إذاً يصبح<bdo dir="ltr">$\frac{\partial L}{\partial X}$</bdo> هو الـloss gradient الخاص بالـlayer السابقة، وبالتالي بنحسبه عشان نكمل الـbackpropagation في باقي الـlayers.
        <bdo dir="ltr">
            $$
            \frac{\partial L}{\partial X} = \frac{\partial O}{\partial X} × \frac{\partial L}{\partial O}
            $$ 
        </bdo>
    </h4>
    <p align="center">
        <img src="/lib/img/why calculate local gradients in cnn.gif" alt="why calculate local gradients in cnn"/>
        <br>
        Fig 4: Why do we need to calculate <bdo dir="ltr">$\frac{\partial L}{\partial X}, \frac{\partial L}{\partial F}$</bdo>
    </p>
    <h3>
        إزاي هنحسب <bdo dir="ltr">$\frac{\partial L}{\partial F}$</bdo>؟
    </h3>
    <h4>
        في البداية عشان نحسب <bdo dir="ltr">$\frac{\partial L}{\partial F}$</bdo>، هنحتاج نحسب الـlocal gradient الخاص بيها وهو <bdo dir="ltr">$\frac{\partial O}{\partial F}$</bdo>، العلاقة بين O و F الى هنحسب منها الـgradient هى:
        <bdo dir="ltr">
            $$\underbrace{\left( \begin{array}{cc}
            O_{11}&O_{12} \\
            O_{21}&O_{22} \\
            \end{array} \right)}_{O_{2×2}} = 
            \underbrace{\left( \begin{array}{ccc}
            X_{11}&X_{12}&X_{13} \\
            X_{21}&X_{22}&X_{23} \\
            X_{31}&X_{32}&X_{33} \\
            \end{array} \right)}_{X_{3×3}} \otimes
            \underbrace{\left( \begin{array}{cc}
            F_{11}&F_{12} \\
            F_{21}&F_{22} \\
            \end{array} \right)}_{F_{2×2}}
            $$ 
        </bdo>
        عشان نحسب <bdo dir="ltr">$\frac{\partial O}{\partial F}$</bdo> لازم نحسب الـgradient الخاص بـO بالنسبة لكل element في F، على سبيل المثال لو حسبنا الـgradient الخاص بـO بالنسبة إلى <bdo dir="ltr">$F_{11}$</bdo>:
        <bdo dir="ltr">
            $$
            O_{11} = X_{11}F_{11} + X_{12}F_{12} + X_{21}F_{21} + X_{22}F_{22} \\ \rule{0mm}{7mm}
            O_{12} = X_{12}F_{11} + X_{13}F_{12} + X_{22}F_{21} + X_{23}F_{22} \\ \rule{0mm}{7mm}
            O_{21} = X_{21}F_{11} + X_{22}F_{12} + X_{31}F_{21} + X_{32}F_{22} \\ \rule{0mm}{7mm}
            O_{22} = X_{22}F_{11} + X_{23}F_{12} + X_{32}F_{21} + X_{33}F_{22} \\ \rule{0mm}{10mm}
            \frac{\partial O_{11}}{\partial F_{11}} = X_{11}, \hspace{5mm}
            \frac{\partial O_{12}}{\partial F_{11}} = X_{12}, \hspace{5mm}
            \frac{\partial O_{21}}{\partial F_{11}} = X_{21}, \hspace{5mm}
            \frac{\partial O_{22}}{\partial F_{11}} = X_{22}\\ 
            \rule{0mm}{10mm}
            \therefore \frac{\partial O}{\partial F_{11}} = \left( \begin{array}{cc}
            \frac{\partial O_{11}}{\partial F_{11}}&\frac{\partial O_{12}}{\partial F_{11}} \\\rule{0mm}{5mm}
            \frac{\partial O_{21}}{\partial F_{11}}&\frac{\partial O_{22}}{\partial F_{11}} \\
            \end{array} \right) =
            \left( \begin{array}{cc}
            X_{11}&X_{12} \\
            X_{21}&X_{22}
            \end{array} \right)
            $$ 
        </bdo>
        وبالمثل نقدر نحسب الـgradient بالنسبة لكل من <bdo dir="ltr">$F_{21}, F_{12}, F_{22}$</bdo>:
        <bdo dir="ltr">
            $$
            \frac{\partial O}{\partial F_{12}} = 
            \left( \begin{array}{cc}
            X_{12}&X_{13} \\
            X_{22}&X_{23}
            \end{array} \right), \hspace{5mm}
            \frac{\partial O}{\partial F_{21}} = 
            \left( \begin{array}{cc}
            X_{21}&X_{22} \\
            X_{31}&X_{32}
            \end{array} \right), \hspace{5mm}
            \frac{\partial O}{\partial F_{22}} = 
            \left( \begin{array}{cc}
            X_{22}&X_{23} \\
            X_{32}&X_{33}
            \end{array} \right)
            $$ 
        </bdo>
        دلوقتي نقدر نطبق قاعدة الـChain rule عشان نحسب <bdo dir="ltr">$\frac{\partial L}{\partial F}$</bdo>:
        <bdo dir="ltr">
            $$
            \frac{\partial L}{\partial F} = \frac{\partial O}{\partial F} × \frac{\partial L}{\partial O}
            $$ 
        </bdo>
    </h4>
    <h3>
        إزاي هنطبق الـchain rule على مصفوفات؟
    </h3>
    <h4>
        عشان نحسب المعادلة دى، هنحسب الـ loss gradient بالنسبة لكل element في F على حدة باستخدام المعادلة الآتية:
        <bdo dir="ltr">
            $$
            \frac{\partial L}{\partial F_{ij}} = \left\langle \frac{\partial L}{\partial O}, \frac{\partial O}{\partial F_{ij}} \right\rangle_{F}
            $$ 
        </bdo>
        حيث أن <bdo dir="ltr">$\langle\cdot,\cdot\rangle_{F}$</bdo> تعبّر عن الـFrobenius inner product بين مصفوفتين.
{{< admonition type=tip title="ماهو Frobenius Inner Product؟" open=false >}}
<bdo dir="ltr">
$$
\text{Let }
A = \left( \begin{array}{cc}
A_{11}&A_{12} \\
A_{21}&A_{22}
\end{array} \right), \hspace{5mm}
B = \left( \begin{array}{cc}
B_{11}&B_{12} \\
B_{21}&B_{22}
\end{array} \right) \\ \rule{0mm}{8mm}
\therefore \langle A,B\rangle_{F} = A_{11}B_{11} + A_{12}B_{12} + A_{21}B_{21} + A_{22}B_{22}
$$
</bdo>
{{< /admonition >}}
        <bdo dir="ltr">$\frac{\partial L}{\partial O}$</bdo> ممكن نحسبه بسهولة عن طريق تفاضل الـloss function بالنسبة لكل عنصر في O لو كان O هو ناتج آخر layer في الـnetwork، أما لو كان ناتج layer في وسط الnetwork هيكون عبارة عن الـloss gradient القادم من الـlayer التالية لها.
        <bdo dir="ltr">
            $$
            \frac{\partial L}{\partial O} = \left( \begin{array}{cc}
            \frac{\partial L}{\partial O_{11}}&\frac{\partial L}{\partial O_{12}} \\ \rule{0mm}{5mm}
            \frac{\partial L}{\partial O_{21}}&\frac{\partial L}{\partial O_{22}} \\
            \end{array} \right)
            $$
        </bdo>
        الآن نقدر نحسب الـloss gradient بالنسبة لكل عنصر في F:
        <bdo dir="ltr">
            $$
            \frac{\partial L}{\partial F_{11}} = \frac{\partial L}{\partial O_{11}} \frac{\partial O_{11}}{\partial F_{11}} + \frac{\partial L}{\partial O_{12}} \frac{\partial O_{12}}{\partial F_{11}} + \frac{\partial L}{\partial O_{21}} \frac{\partial O_{21}}{\partial F_{11}} + \frac{\partial L}{\partial O_{22}} \frac{\partial O_{22}}{\partial F_{11}} \\ 
            \rule{0mm}{10mm}
            \frac{\partial L}{\partial F_{12}} = \frac{\partial L}{\partial O_{11}} \frac{\partial O_{11}}{\partial F_{12}} + \frac{\partial L}{\partial O_{12}} \frac{\partial O_{12}}{\partial F_{12}} + \frac{\partial L}{\partial O_{21}} \frac{\partial O_{21}}{\partial F_{12}} + \frac{\partial L}{\partial O_{22}} \frac{\partial O_{22}}{\partial F_{12}} \\ 
            \rule{0mm}{10mm}
            \frac{\partial L}{\partial F_{21}} = \frac{\partial L}{\partial O_{11}} \frac{\partial O_{11}}{\partial F_{21}} + \frac{\partial L}{\partial O_{12}} \frac{\partial O_{12}}{\partial F_{21}} + \frac{\partial L}{\partial O_{21}} \frac{\partial O_{21}}{\partial F_{21}} + \frac{\partial L}{\partial O_{22}} \frac{\partial O_{22}}{\partial F_{21}} \\ 
            \rule{0mm}{10mm}
            \frac{\partial L}{\partial F_{22}} = \frac{\partial L}{\partial O_{11}} \frac{\partial O_{11}}{\partial F_{22}} + \frac{\partial L}{\partial O_{12}} \frac{\partial O_{12}}{\partial F_{22}} + \frac{\partial L}{\partial O_{21}} \frac{\partial O_{21}}{\partial F_{22}} + \frac{\partial L}{\partial O_{22}} \frac{\partial O_{22}}{\partial F_{22}} \\ 
            \rule{0mm}{5mm}
            $$
        </bdo>
        لو عوّضنا عن كل <bdo dir="ltr">$\frac{\partial O_{xy}}{\partial F_{ij}}$</bdo> بالقيم الى حسبناها فوق:
        <bdo dir="ltr">
            $$
            \frac{\partial L}{\partial F_{11}} = \frac{\partial L}{\partial O_{11}} X_{11} + \frac{\partial L}{\partial O_{12}} X_{12} + \frac{\partial L}{\partial O_{21}} X_{21} + \frac{\partial L}{\partial O_{22}} X_{22} \\ 
            \rule{0mm}{10mm}
            \frac{\partial L}{\partial F_{12}} = \frac{\partial L}{\partial O_{11}} X_{12} + \frac{\partial L}{\partial O_{12}} X_{13} + \frac{\partial L}{\partial O_{21}} X_{22} + \frac{\partial L}{\partial O_{22}} X_{23} \\ 
            \rule{0mm}{10mm}
            \frac{\partial L}{\partial F_{21}} = \frac{\partial L}{\partial O_{11}} X_{21} + \frac{\partial L}{\partial O_{12}} X_{22} + \frac{\partial L}{\partial O_{21}} X_{31} + \frac{\partial L}{\partial O_{22}} X_{32} \\ 
            \rule{0mm}{10mm}
            \frac{\partial L}{\partial F_{22}} = \frac{\partial L}{\partial O_{11}} X_{22} + \frac{\partial L}{\partial O_{12}} X_{23} + \frac{\partial L}{\partial O_{21}} X_{32} + \frac{\partial L}{\partial O_{22}} X_{33} \\ 
            \rule{0mm}{10mm}
            $$
        </bdo>
        هل لاحظت إحنا بنعمل إيه؟ لاحظ إن ناتج الـchain rule الى طبقناها مشابه لناتج عملية الـconvolution الى كنا بنطبقها في الـforward pass! تعالى نكتب المعادلات الى وصلنا لها بصيغة convolution operation عشان نتأكد:
        <bdo dir="ltr">
            $$
            \underbrace{\left( \begin{array}{cc}
            \frac{\partial L}{\partial F_{11}}&\frac{\partial L}{\partial F_{12}} \\ \rule{0mm}{5mm}
            \frac{\partial L}{\partial F_{21}}&\frac{\partial L}{\partial F_{22}} \\
            \end{array} \right)}_{\frac{\partial L}{\partial F}} = 
            \underbrace{\left( \begin{array}{ccc}
            X_{11}&X_{12}&X_{13} \\
            X_{21}&X_{22}&X_{23} \\
            X_{31}&X_{32}&X_{33} \\
            \end{array} \right)}_{X_{3×3}} \otimes
            \underbrace{\left( \begin{array}{cc}
            \frac{\partial L}{\partial O_{11}}&\frac{\partial L}{\partial O_{12}} \\ \rule{0mm}{5mm}
            \frac{\partial L}{\partial O_{21}}&\frac{\partial L}{\partial O_{22}} \\
            \end{array} \right)}_{\frac{\partial L}{\partial O}}
            $$
        </bdo>
        ناتج الـconvolution هو الأربع معادلات الى وصلنا لهم، وده يخلينا نستنتج إن الـconv layer بتطبق الـconvolution operation في الـforward pass عشان تحسب الـfeature maps، وفي الـbackward pass عشان تحسب الـgradients.
    </h4>
    <h3>
        بعد ما حسبنا <bdo dir="ltr">$\frac{\partial L}{\partial F}$</bdo> هنشوف دلوقتي إزاي نحسب <bdo dir="ltr">$\frac{\partial L}{\partial X}$</bdo>
    </h3>
    <h4>
        <bdo dir="ltr">
            $$\underbrace{\left( \begin{array}{cc}
            O_{11}&O_{12} \\
            O_{21}&O_{22} \\
            \end{array} \right)}_{O_{2×2}} = 
            \underbrace{\left( \begin{array}{ccc}
            X_{11}&X_{12}&X_{13} \\
            X_{21}&X_{22}&X_{23} \\
            X_{31}&X_{32}&X_{33} \\
            \end{array} \right)}_{X_{3×3}} \otimes
            \underbrace{\left( \begin{array}{cc}
            F_{11}&F_{12} \\
            F_{21}&F_{22} \\
            \end{array} \right)}_{F_{2×2}}
            $$ 
        </bdo>
        بنفس الطريقة الى حسبنا بيها الـlocal gradient بالنسبة لـF، هنحسب الـlocal gradient بالنسبة لـX:
        <bdo dir="ltr">
            $$
            O_{11} = X_{11}F_{11} + X_{12}F_{12} + X_{21}F_{21} + X_{22}F_{22} \\ \rule{0mm}{7mm}
            O_{12} = X_{12}F_{11} + X_{13}F_{12} + X_{22}F_{21} + X_{23}F_{22} \\ \rule{0mm}{7mm}
            O_{21} = X_{21}F_{11} + X_{22}F_{12} + X_{31}F_{21} + X_{32}F_{22} \\ \rule{0mm}{7mm}
            O_{22} = X_{22}F_{11} + X_{23}F_{12} + X_{32}F_{21} + X_{33}F_{22} \\ \rule{0mm}{10mm}
            \frac{\partial O_{11}}{\partial X_{11}} = F_{11}, \hspace{5mm}
            \frac{\partial O_{12}}{\partial X_{11}} = 0 \\ \rule{0mm}{8mm}
            \frac{\partial O_{21}}{\partial X_{11}} = 0,  \hspace{8mm}
            \frac{\partial O_{22}}{\partial X_{11}} = 0 \\
            \rule{0mm}{10mm}
            \therefore \frac{\partial O}{\partial X_{11}} = \left( \begin{array}{ccc}
            \frac{\partial O_{11}}{\partial X_{11}}&\frac{\partial O_{12}}{\partial X_{11}} \\\rule{0mm}{5mm}
            \frac{\partial O_{21}}{\partial X_{11}}&\frac{\partial O_{22}}{\partial X_{11}} \\
            \end{array} \right) =
            \left( \begin{array}{cc}
            F_{11}&0 \\
            0&0
            \end{array} \right)
            $$ 
        </bdo>
        وبالمثل نقدر نحسب الـgradient بالنسبة لكل من لباقي عناصر X:
        <bdo dir="ltr">
            $$
            \frac{\partial O}{\partial X_{12}} = 
            \left( \begin{array}{cc}
            F_{12}&F_{11} \\
            0&0
            \end{array} \right), \hspace{5mm}
            \frac{\partial O}{\partial X_{13}} = 
            \left( \begin{array}{cc}
            0&F_{12} \\
            0&0
            \end{array} \right), \hspace{5mm}
            \frac{\partial O}{\partial X_{21}} = 
            \left( \begin{array}{cc}
            F_{21}&0 \\
            F_{11}&0
            \end{array} \right) \text{  ... etc}
            $$ 
        </bdo>
        بعد ما حسبنا الـlocal gradient بالنسبة لـX نقدر دلوقتي نطبق الـchain rule:
        <bdo dir="ltr">
            $$
            \frac{\partial L}{\partial X_{ij}} = \langle \frac{\partial L}{\partial O}, \frac{\partial O}{\partial X_{ij}} \rangle_{F}
            $$ 
        </bdo>
        بعد ما نحسب الـproduct ونعوّض هنلاقي النتائج التالية:
        <bdo dir="ltr">
            $$
            \frac{\partial L}{\partial X_{11}} = \frac{\partial L}{\partial O_{11}} F_{11} 
            \hspace{80mm} \\ \rule{0mm}{8mm}
            \frac{\partial L}{\partial X_{12}} = \frac{\partial L}{\partial O_{11}} F_{12} + \frac{\partial L}{\partial O_{12}} F_{11}
            \hspace{62mm} \\ \rule{0mm}{8mm}
            \frac{\partial L}{\partial X_{13}} = \frac{\partial L}{\partial O_{12}} F_{12}
            \hspace{80mm} \\ \rule{0mm}{8mm}
            \frac{\partial L}{\partial X_{21}} = \frac{\partial L}{\partial O_{11}} F_{21} + \frac{\partial L}{\partial O_{21}} F_{11}
            \hspace{62mm} \\ \rule{0mm}{8mm}
            \frac{\partial L}{\partial X_{22}} = \frac{\partial L}{\partial O_{11}} F_{22} + \frac{\partial L}{\partial O_{12}} F_{21} + \frac{\partial L}{\partial O_{21}} F_{12} + \frac{\partial L}{\partial O_{22}} F_{11}
            \hspace{27mm} \\ \rule{0mm}{8mm}
            \frac{\partial L}{\partial X_{23}} = \frac{\partial L}{\partial O_{12}} F_{22} + \frac{\partial L}{\partial O_{22}} F_{12}
            \hspace{62mm} \\ \rule{0mm}{8mm}
            \frac{\partial L}{\partial X_{31}} = \frac{\partial L}{\partial O_{21}} F_{21}
            \hspace{80mm} \\ \rule{0mm}{8mm}
            \frac{\partial L}{\partial X_{32}} = \frac{\partial L}{\partial O_{21}} F_{22} + \frac{\partial L}{\partial O_{22}} F_{21}
            \hspace{62mm} \\ \rule{0mm}{8mm}
            \frac{\partial L}{\partial X_{33}} = \frac{\partial L}{\partial O_{22}} F_{22}
            \hspace{80mm}
            $$ 
        </bdo>
    </h4>
    <h3>
        المعادلات شكلها مختلف عن الى شفناه بالنسبة لـF، بس هل ممكن نعبّر عنها بـconvolution؟
    </h3>
    <h4>
        نعم، نقدر نعبّر عنها عن طريق 'Full' Covolution بين F و <bdo dir="ltr">$\frac{\partial L}{\partial O}$</bdo>
    </h4>
{{< admonition type=Note title="Convolution Vs. Cross Correlation" open=true >}}
على الرغم من إن الـconvolutional neural networks تحتوي في اسمها على كلمة convolution، إلا إن المسمى الفعلي للعملية الرياضية الى بتحصل فيها هو Cross Correlation, وهى إننا بنحرك الـfilter كsliding window على الـinput ونضرب العناصر المقابلة في بعضها ونجمع النتيجة، أما في الـConvolution بندوّر الـfilter بـ180 درجة الأول قبل ما نحركه.
{{< /admonition >}}
    <h4>
        عشان نطبق الـconvolution بالشكل الى هيطلع لي المعادلات دى لازم ندوّر الفلتر 180 درجة:
    <bdo dir="ltr">
        $$
        \left( \begin{array}{cc}
        F_{11}&F_{12} \\
        F_{21}&F_{22} \\
        \end{array} \right)
        \xRightarrow{180^o \text{-Rotation}}
        \left( \begin{array}{cc}
        F_{22}&F_{21} \\
        F_{12}&F_{11} \\
        \end{array} \right)
        $$ 
    </bdo>
    دلوقتي نقدر نحسب <bdo dir="ltr">$\frac{\partial L}{\partial X}$</bdo> باستخدام full convolution، وهو يكافئ covolution with full zero padding:
    <bdo dir="ltr">
            $$
            \underbrace{\left( \begin{array}{ccc}
            \frac{\partial L}{\partial X_{11}}&\frac{\partial L}{\partial X_{12}}&\frac{\partial L}{\partial X_{13}}\\ \rule{0mm}{5mm}
            \frac{\partial L}{\partial X_{21}}&\frac{\partial L}{\partial X_{22}}&\frac{\partial L}{\partial X_{23}}\\ \rule{0mm}{5mm}
            \frac{\partial L}{\partial X_{31}}&\frac{\partial L}{\partial X_{23}}&\frac{\partial L}{\partial X_{33}}\\
            \end{array} \right)}_{\frac{\partial L}{\partial X}} = 
            \underbrace{\left( \begin{array}{cccc}
            0&0&0&0 \\ \rule{0mm}{5mm}
            0&\frac{\partial L}{\partial O_{11}}&\frac{\partial L}{\partial O_{12}}&0 \\ \rule{0mm}{5mm}
            0&\frac{\partial L}{\partial O_{21}}&\frac{\partial L}{\partial O_{21}}&0 \\ \rule{0mm}{5mm}
            0&0&0&0
            \end{array} \right)}_{padded \frac{\partial L}{\partial O}} \otimes
            \underbrace{\left( \begin{array}{cc}
            F_{22}&F_{21} \\
            F_{12}&F_{11} \\
            \end{array} \right)}_{Rotated \hspace{1mm} F}
            $$ 
        </bdo>
    </h4>
    <p align="center">
        <img src="/lib/img/full convolution operation.gif" alt="full convolution operation"/>
        <br>
        Fig 5: Full Convolution operation visualized between 180-degree flipped Filter F and loss gradient <bdo dir="ltr">$\frac{\partial L}{\partial O}$</bdo>
    </p>
    <h3>
        هل الـpooling layers بتأثر على الـbackpropagation؟
    </h3>
    <h4>
        الـPooling layers لا تحتوي على learnable parametersو لكن زى ما لها دور في الـforward pass فبالتأكيد لها دور في الـbackward pass اثناء حساب الـgradients.
        <br>
        لنفترض إن الـinput الخاص بـ(2×2)pooling layer هو المصفوفة A, والـOutput هو المصفوفة B:
    <bdo dir="ltr">
        $$
        A = \left( \begin{array}{cccc}
            A_{11}&A_{12}&A_{13}&A_{14} \\
            A_{21}&A_{22}&A_{23}&A_{24} \\
            A_{31}&A_{32}&A_{33}&A_{34} \\
            A_{41}&A_{42}&A_{43}&A_{44} \\
            \end{array} \right), \hspace{5mm}
        B = \left( \begin{array}{cc}
            B_{11}&B_{12} \\
            B_{21}&B_{22}\\
            \end{array} \right) \\ \rule{0mm}{7mm}
        B_{11} = pool\left( \begin{array}{cc}
            A_{11}&A_{12} \\
            A_{21}&A_{22}\\
            \end{array} \right), \hspace{2mm}
        B_{12} = pool\left( \begin{array}{cc}
            A_{13}&A_{14} \\
            A_{23}&A_{24}\\
            \end{array} \right) \hspace{2mm}
        \\ \rule{0mm}{7mm}
        B_{21} = pool\left( \begin{array}{cc}
            A_{31}&A_{32} \\
            A_{41}&A_{42}\\
            \end{array} \right), \hspace{2mm}
        B_{22} = pool\left( \begin{array}{cc}
            A_{33}&A_{34} \\
            A_{43}&A_{44}\\
            \end{array} \right) \hspace{2mm}
        $$ 
    </bdo>
    أثناء الـbackpropagation هيبقى عندي <bdo dir="ltr">$\frac{\partial L}{\partial B}$</bdo> ومنه هحتاج أحسب <bdo dir="ltr">$\frac{\partial L}{\partial A}$</bdo> عشان يروح للـlayer الى وراه:
    <bdo dir="ltr">
        $$
        \frac{\partial L}{\partial B} =
         \left( \begin{array}{cc}
        \frac{\partial L}{\partial B_{11}}&\frac{\partial L}{\partial B_{12}} \\ \rule{0mm}{5mm}
        \frac{\partial L}{\partial B_{21}}&\frac{\partial L}{\partial B_{22}}
        \end{array} \right)
        $$ 
    </bdo>
    في حالة الـ Max pooling, عنصر واحد فقط من الأربعة هو الى بيعدي للـlayer التالية، وبالتالي هو العنصر الوحيد الى بيكون عنده قيمة للـgradient لإنه هو الوحيد الى شارك في الـerror.
    <br>
    لو أفترضنا إن <bdo dir="ltr">$A_{11},A_{13},A_{31},A_{33}$</bdo> هم أكبر قيم في كل cell:
    <bdo dir="ltr">
        $$
        \therefore \frac{\partial L}{\partial A} = \left( \begin{array}{cccc}
            \frac{\partial L}{\partial B_{11}}&0&\frac{\partial L}{\partial B_{12}}&0 \\ \rule{0mm}{5mm}
            0&0&0&0 \\ \rule{0mm}{5mm}
            \frac{\partial L}{\partial B_{21}}&0&\frac{\partial L}{\partial B_{22}}&0 \\ \rule{0mm}{5mm}
            0&0&0&0\\
            \end{array} \right)
        $$ 
    </bdo>
    أما في حالة الـAverage pooling، فكل عنصر بيشارك في الـerror بنفس القيمة، فبالتالي الـgradient بيتوزع عليهم بالتساوي:
    <bdo dir="ltr">
        $$
        \therefore \frac{\partial L}{\partial A} = \frac{1}{4} \left( \begin{array}{cccc}
            \frac{\partial L}{\partial B_{11}}&\frac{\partial L}{\partial B_{11}}&\frac{\partial L}{\partial B_{12}}&\frac{\partial L}{\partial B_{12}} \\ \rule{0mm}{5mm}
            \frac{\partial L}{\partial B_{11}}&\frac{\partial L}{\partial B_{11}}&\frac{\partial L}{\partial B_{12}}&\frac{\partial L}{\partial B_{12}} \\ \rule{0mm}{5mm}
            \frac{\partial L}{\partial B_{21}}&\frac{\partial L}{\partial B_{21}}&\frac{\partial L}{\partial B_{22}}&\frac{\partial L}{\partial B_{22}} \\ \rule{0mm}{5mm}
            \frac{\partial L}{\partial B_{21}}&\frac{\partial L}{\partial B_{21}}&\frac{\partial L}{\partial B_{22}}&\frac{\partial L}{\partial B_{22}}\\ 
            \end{array} \right)
        $$ 
    </bdo>
    </h4>
    <h3>
        إذاً في النهاية ممكن نلخص الـbackpropagation في الـCNNs  في خطوتين(بالإضافة إلى تأثير الـpooling على الـgradient flow):
    </h3>
    <bdo dir="ltr">
        $$
        \frac{\partial L}{\partial F} = Convolution(\text{Input } X, \text{ Loss gradient } \frac{\partial L}{\partial O}) 
        \hspace{25mm} \\ \rule{0mm}{8mm}
        \frac{\partial L}{\partial x} = \text{Full Convolution}(180^o \text{ Rotated filter } F, \text{ Loss gradient } \frac{\partial L}{\partial O})
        $$ 
    </bdo>
</div>


<br>
<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">

<div dir="rtl" style="text-align: justify">
    <b>
    لمزيد من المعلومات:
    </b>
</div>

* [Convolutions and Backpropagations](https://pavisj.medium.com/convolutions-and-backpropagations-46026a8f5d2c)
* [Backpropagation In Convolutional Neural Networks](https://www.jefkine.com/general/2016/09/05/backpropagation-in-convolutional-neural-networks/)
<br>

<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
