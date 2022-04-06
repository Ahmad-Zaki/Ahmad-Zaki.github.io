# Image Super-Resolution


<div dir="rtl" style="text-align: justify">
    <h4>
        Image Super-resolution (SR) هو الإسم المتداول لعملية تحويل الصورة من Low resolution إلى high resolution عن طريق زيادة عدد الـpixels في الصورة بحيث تحافظ على التفاصيل الموجودة فيها، وهي من أكثر التقنيات أهمية في مجال الـImage processing والـcomputer vision وتدخل في العديد من التطبيقات في الواقع زي تحسين الصور الطبية (Medical Imaging) وصور الأقمار الصناعية وكاميرات المراقبة الـlive-streaming وغيرها الكثير.
    </h4>
</div>

<!--more-->

<p align="center">
    <img src="/lib/img/SISR - headline.png" alt="Super Resolution Output"/>
    <br>
    Fig 1: Original low-resolution image (right) and its generated high-resolution (Super resolved) counterpart (left)
</p>

<div dir="rtl" style="text-align: justify">
    <h4>
ممكن نمثل عملية الـsuper resolution عن طريق الـdiagram الموجود في Figure 2، الـdiagram بيوضح إن الصورة الـlow resolution جاية عن طريق تطبيق عملية downsampling على صورة high resolution بالإضافة لبعض الـnoise، فبالتالي أي موديل هنعمله بيحاول يتعلم يعكس العملية دى عشان يوصل من الـlow resolution للـhigh resolution.
    </h4> 
        <p align="center">
            <img src="/lib/img/Sketch of the overall framework of SISR.JPG" alt="Sketch of the overall framework of SISR"/>
            <br>
            Fig 2: Sketch of the overall framework of SISR
            <br> 
            Source: <a href="https://arxiv.org/abs/1808.03344">Click here</a> 
        </p>
    <h4>
        التحدي في مشكلة الـsuper resolution هو أنها تعتبر ill-posed problem، بمعنى إن فيه عدد كبير من الصور الـhigh resolution ممكن تطلعه من أي صورة low resolution، ومفيش أي طريقة متاحة تقدر تحكم بيها وتقول مين أفضل واحدة إلا لو معاك الصورة الـhigh resolution الأصلية، لذلك في الـproduction مفيش metric ممكن تتابعه وتحكم على آداء الموديل من خلاله.
    </h4>    
    <h3>
     الطرق التقليدية في الـsuper Resolution
    </h3>
    <h4>
    الـsuper resolution كان موجود من فترة كبيرة في الـimage processing قبل حتى قدوم الـdeep learning للساحة تحت مسمى image interpolation، فيه أكثر من طريقة ممكن نعمل بيها interpolation زى الـnearest neighbour، bilinear، bicubic interpolations، الطرق دى بتتنفذ بمعادلات رياضية مباشرة ومش بتحتاج تتعلم أي parameters قبل ما تشتغل، وبالرغم من إنها سريعة ومباشرة إلا إن نتايجها كان غير مرضية وقد تكون غير مقبولة.
    </h4> 
        <p align="center">
            <img src="/lib/img/Comparison_of_1D_and_2D_interpolation.png" alt="Image Interpolation"/>
            <br>
            Fig 3: Comparison between NN, Bilinear, and Bicubic Interpolation
            <br> 
            Source: <a href="https://en.wikipedia.org/wiki/Bicubic_interpolation">Click here</a> 
        </p>
    <h3>
        استخدام الـdeep learning في الـsuper resolution
    </h3>
    <h4>
        أول محاولات تطبيق الـdeep learning في الـsuper resolution كانت في 2014 عن طريق <a href="https://arxiv.org/abs/1501.00092">SRCNN Model</a>، الموديل البسيط الموضّح في figure 4 بياخد ناتج أحد الطرق التقليدية زى الـbicubic interpolation ويمرره على 3 conv layers عشان يحسن نتيجته.
        </h4> 
        <p align="center">
            <img src="/lib/img/Sketch of the SRCNN architecture.JPG" alt="SRCNN architecture"/>
            <br>
            Fig 4: Sketch of the SRCNN architecture
            <br> 
            Source: <a href="https://arxiv.org/abs/1808.03344">Click here</a> 
        </p>
        <h4>
        الموديل بيستخدم mean square error كـloss function وقدر يحقق state of the art results في وقت ظهوره في 2015 لكنه يحتوي بعض العيوب:
        <ol>
            <li>استخدام bicubic interpolation بينتج smooth images وبالتالي الموديل مش بيقدر يتعلم الـdetails الموجودة في الصورة وممكن يؤدي إلى نتايج غير مرضية.</li>
            <li>استخدام اي نوع من الـinterpolation بيقيد آداء الموديل بشكل كبير، لإنها بتفترض إن كل الصورة low resolution بتكون ناتجة من نفس الـdownsampling operation لكن في الواقع ده غير صحيح.</li>
            <li>الموديل بيشتغل على صورة بحجم الـhigh resolution وهي 4 أضعاف حجم الـlow resolution (لو إفترضنا إن الـscaling factor=4) وبالتالي عدد الـoperations الى الموديل بينفذها بيزيد بشكل كبير جداّ مع زيادة حجم الـlow resolution image.</li>
        </ol>
        <br>
        أحد حلول المشاكل دى هو إننا نعمل upsampling في آخر الموديل، ويكون عن طريق trained weights بدل الطرق التقليدية، كده الموديل يقدر يتعلم الـupsampling الى هو محتاجه وبالتالي تفاديت قيود الـinterpolation، وكمان الموديل دلوقتي بيتعامل مع الصورة الـlow resolution بس وبالتالي عدد الـoperations الى بيعملها أصبح أقل.
        </h4>
        <p align="center">
            <img src="/lib/img/post-upsampling.jpg" alt="Post-upsampling Technique"/>
            <br>
            Fig 5: Post-upsampling Technique
            <br> 
            Source: <a href="https://www.analyticsvidhya.com/blog/2021/05/deep-learning-for-image-super-resolution/">Click here</a> 
        </p>
    <h3>
طرق الـupsampling المستخدمة في الـdeep learning
    </h3>
    <h4>
        الطريقة الأولى تتم باستخدام deconvolution layer أو transposed convolution layer، وفيها بنعمل upsampling للـinput feature map عن طريق nearest neighbour وبعدها نمررها على convolution layer (لاحظ Figure 6)، كده نقدر نسخدم learnable weights في الـupsampling وكمان أصبحت العملية أسرع لإن الـnearest neighbour upsampling بسيط ومفيش فيه أي حسابات.
        </h4>
        <p align="center">
            <img src="/lib/img/deconvolution operation.JPG" alt="deconvolution operation"/>
            <br>
            Fig 6: Sketch of the deconvolution layer
            <br> 
            Source: <a href="https://arxiv.org/abs/1808.03344">Click here</a> 
        </p>
    <h4>
    المشكلة في الـdeconvolution layer هى إن الـnearest neighbour بيكرر الـpixels الموجودة في الـfeature map وبالتالي بيضيف نوع من الـredundancy وده بيأثر على جودة الـoutput الناتج من الموديل، لذلك ظهرت طريقة آخرى للـupsampling تعتمد بشكل كامل على learnable weights وفي الـefficient sub-pixel convolutions (ESPC)، الفكرة منها هو إن لو عايز أكبّر الصورة ب<bdo dir="ltr">$factor=r$</bdo>، هستخرج feature maps عددها <bdo dir="ltr">$r^{2}$</bdo>، وأرتّب الـpixels المناظرة من كل feature map جنب بعض كما هو موضّح في Figure 7.
    </h4>
        <p align="center">
            <img src="/lib/img/ESPCN.png" alt="Sub-pixel convolution layer"/>
            <br>
            Fig 7: Sub-pixel convolution layer with a scaling factor=2
            <br> 
            Source: <a href="https://krasserm.github.io/2019/09/04/super-resolution/">Click here</a> 
        </p>
    <h3>
الـmodels المستخدمة في الـsuper resolution
    </h3>
    <h4>
        مع استخدام الـdeep learning في الـsuper resolution ظهرت models وarchitectures كثيرة جداً، بعض الأمثلة موضّحة في Figure 8. كل واحد منهم محتاج مساحة غير صغيرة من المقال لتغطية تفاصيله لذلك هكتفي بالاتنين الى طبقتهم وهم SRResNet و الـEDSR.
    </h4>
        <p align="center">
            <img src="/lib/img/SISR models.JPG" alt="SISR models"/>
            <br>
            Fig 8: Sketch of several deep architectures for SISR
            <br> 
            Source: <a href="https://arxiv.org/abs/1808.03344">Click here</a> 
        </p>
    <h3>
        <a href="https://arxiv.org/abs/1609.04802">SRResNet Model</a> 
    </h3>
    <h4>
        الـResNet architecture قدمت حل سحري لمشكلة الـvanishing gradients وأتاحت لنا القدرة على تدريب deep models بدون مشاكل، الـSRResNet بتتبنى نفس الفكرة مع بعض الإضافات لتلائم الـsuper resolution، كما هو موضح في figure 8 (a)، الموديل مكوّن من 16 res block، كل block يحتوى على اتنين conv layer واتنين batch normalization layer بينهم Parametric ReLU، وفي الـoutput بيستخدم اتنين ESPCN بـscaling factor 2 عشان يضاعف حجم الصورة 4 مرّات، وبيستخدم mean square error loss function موضّحة كالآتي:
        <bdo dir="ltr">
            $$
            l_{MSE} = \frac{1}{r^{2}WH}\sum^{rW}_{x=1}\sum^{rH}_{y=1}(I^{HR}_{x,y} - I^{SR}_{x,y})^2
            $$ 
        </bdo>
      حيث H,W هما طول وعرض الصورة الـlow resolution، وr هو الـscaling factor، و <bdo dir="ltr">$I^{HR}$</bdo> هي الصورة الـhigh resolution الأصلية، و <bdo dir="ltr">$I^{SR}$</bdo> هي الصورة الناتجة من الموديل.
      <br><br>
      لو عايزين نبني الموديل بالكود، في البداية هعمل function بتبني الـres block:
    </h4>
</div>

```python
from tensorflow.keras.layers import BatchNormalization, Add,bConv2D, PReLU
from tensorflow.keras.initializers import Constant

def PReLU_activation(name):
    return PReLU(Constant(value=0.25), shared_axes=[1,2], name=name)

def residual_block(layer_input, filters, block_number):
    """Residual block described in paper"""
    d = Conv2D(filters, kernel_size=3, strides=1, padding='same', name=f"conv_res_{block_number}_1")(layer_input)
    d = PReLU_activation(f"prelu_res_{block_number}")(d)
    d = BatchNormalization(momentum=0.8, name=f"BN_res_{block_number}_1")(d)
    d = Conv2D(filters, kernel_size=3, strides=1, padding='same', name=f"conv_res_{block_number}_2")(d)
    d = BatchNormalization(momentum=0.8, name=f"BN_res_{block_number}_2")(d)
    d = Add(name=f"add_res_{block_number}")([d, layer_input])
    return d
```

<div dir="rtl" style="text-align: justify">
    <h4>
        بعد كده هنعمل الـupsampling operation، لكن keras مفيش فيه ESPCN layer لذلك هنسخدم conv layer عادية ونعمل بعدها pixel shuffle عن طريق depth to space layer الموجودة في tensorflow:
    </h4>
</div>

```python
from tensorflow.nn import depth_to_space

def upsample_block(layer_input, scale, i) :
    u = Conv2D(256, kernel_size=3, strides=1, padding='same', name=f"conv_up_{i}")(layer_input)
    u = depth_to_space(u, scale, name=f"pix_shuf_{i}")
    u = PReLU_activation(name=f"prelu_up_{i}")(u)
    return u
```

<div dir="rtl" style="text-align: justify">
    <h4>
        الآن هنبني الموديل بالكامل، ممكن تراجع الـdiagram الخاص بالموديل من الـpaper بتاعته، والموضّح في Figure 9:
    </h4>
        <p align="center">
            <img src="/lib/img/SRResNet.JPG" alt="SRResNet model"/>
            <br>
            Fig 9: SRResNet Model Architecture as descriped in paper
            <br> 
            Source: <a href="https://arxiv.org/abs/1609.04802">Click here</a> 
        </p>
</div>

```python
from tensorflow.keras.layers import Input
from tensorflow.keras.models import Model
NUM_RES_BLOCKS = 16

lr_image = Input(shape=(None, None, 3))
c1 = Conv2D(64, kernel_size=9, strides=1, padding='same', name="Conv_ip")(lr_image)
c1 = r = PReLU_activation(name="prelu_ip")(c1)

for i in range(NUM_RES_BLOCKS):
    r = residual_block(r, 64, i)

c2 = Conv2D(64, kernel_size=3, strides=1, padding='same', name="conv_out")(r)
c2 = BatchNormalization(momentum=0.8, name="BN_out")(c2)
c2 = Add(name="add_out")([c2, c1])

u1 = upsample_block(c2, 2, 1)
u2 = upsample_block(u1, 2, 2)
c3 = Conv2D(3, kernel_size=9, strides=1, padding='same', activation="sigmoid", name="conv_final")(u2)

srresnet =  Model(lr_image, c3, name="SRResNet")
```

<div dir="rtl" style="text-align: justify">
{{< admonition type=note title="Note" open=true >}}
الـactivation المستخدمة في الـlayer الأخيرة هي sigmoid، لكن الـpaper استخدمت tanh، أنا استخدمت sigmoid لإن الـpreprocessing الى عملته كان scaling للصور بين 0 و 1 لذلك كانت مناسبة أكثر من الـtanh في التطبيق الى انا عملته.
{{< /admonition >}}
    <h4>
ممكن تشوف كود الموديل بالكامل <a href="https://github.com/Ahmad-Zaki/Single_Image_Super_Resolution/blob/master/models.py#L10">هنا</a>.
    </h4>
    <h4>
        الـSRResNet قدر يحقق نتائج ممتازة بفارق كبير عن الطرق السابقة، لكن تظل مشكلة واضحة في الـoutput الخاص بالموديل وهو إنه رغم تحقيقه لنتائج ممتازة جداً في <a href="https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio">الـPSNR</a> و<a href="https://en.wikipedia.org/wiki/Structural_similarity">الـSSIM</a> إلا إن الصورة بيظهر فيها نوع من الـblurring effect.
    </h4>
    <p align="center">
        <img src="/lib/img/SRResNet output.jpg" alt="SRResNet output"/>
        <br>
        Fig 10: SRResNet Output (middle) compared to input (left) and original (right)
    </p>
    <h3>
        <a href="https://arxiv.org/abs/1707.02921">EDSR</a> 
    </h3>
    <h4>
في 2017 ظهر موديل جديد اسمه Enhanced Deep Super-Resolution Network (EDSR)، في الـPaper دى بيقترحوا تعديل في تصميم الـSRResNet وذلك عن طريق استبدال الـPReLU بـReLU والاستغناء عن أي activations خارج الـres block والاستغناء عن الـBatch norm layers تماماً من الموديل. الـPaper بتبرر التعديل ده بإن الـResNet مصممة لحل high level problem وهي الـClassification، في هذا النوع من المشاكل الـshift الناتج من استخدام الـBatch norm ليس له تأثير واضح على دقة الـoutput، أما في حالة low level problems زى الـsuper resolution، فالـinput والـoutput مرتبطين ببعض بشكل كبير وبالتالي الـbatch norm بيأثر على جودة الـoutput.
    </h4>
    <p align="center">
        <img src="/lib/img/EDSR.JPG" alt="EDSR model"/>
        <br>
        Fig 11: Sketch of EDSR Model
        <br> 
        Source: <a href="https://arxiv.org/abs/1808.03344">Click here</a> 
    </p>
    <h3>
        لكن الـbatch normalization بيديني ميزة مهمة جداً وهي الـtraining statibility، هل أقدر أدرّب الموديل بدونه؟
    </h3>
    <h4>
    الـPaper بتقول إن تصميم الموديل بـ16 res block هيقدر يخلينا ندرّبه بدون مشاكل، لكن لو حبينا نعمل موديل أكبر (زى الموضّح في Figure 11) فممكن نعمل scaling للـfeature maps داخل الـres block، ده هيعوّض بعض التأثير الى فقدته لما استغنيت عن الـbatch norm
    </h4>
    <h4>
    التعديل ده رغم إنه بسيط لكن الإستغناء عن الـbatch norm layers لقيت إن الموديل بيستهك 40% memore أقل من الـSRResNet، وده أدى إلى إن الـEDSR بياخد تقريباً نص الوقت الى كان بياخده الـSRResNet في كل Epoch بنفس الـbatch size، بالإضافة إلى إن لو معايا GPU بـmemory كبيرة فممكن استخدم batch size أكبر وأحصل على training time أقل كمان.
    </h4>
    <h4>
    التعديل الثاني هو استبدال الـmean square error بـmean absolute error كـloss function، الـPaper بتبرر التعديل ده بإن الـMAE يؤدي لـbetter convergence near the minimum ولذلك يصل لنتائج أفضل.
    </h4>
    <bdo dir="ltr">
        $$
        l_{MAE} = \frac{1}{r^{2}WH}\sum^{rW}_{x=1}\sum^{rH}_{y=1}|I^{HR}_{x,y} - I^{SR}_{x,y}|
        $$ 
    </bdo>
    <h4>
كود الـEDSR بسيط ومشابه إلى حد كبير للـSRResNet، الى احنا هنطبقه هنا هو الـbase model الموجود في الـpaper وهو عبارة عن 16 res block فقط وبدون scaling. الكود الخاص بالـres block والـupsample block هيكون كالآتي:
    </h4>
</div>

```python
from tensorflow.keras.layers import Add, Conv2D, Lambda 
from tensorflow.nn import depth_to_space

pixel_shuffle = lambda x: depth_to_space(x, 2)

 def residual_block(layer_input, filters, block_number):
    """Residual block described in paper"""
    d = Conv2D(filters, kernel_size=3, strides=1, padding='same', activation='relu', name=f"conv_res_{block_number}_1")(layer_input)
    d = Conv2D(filters, kernel_size=3, strides=1, padding='same', name=f"conv_res_{block_number}_2")(d)
    d = Add(name=f"add_res_{block_number}")([d, layer_input])
    return d

def upsample_block(layer_input, i) :
    u = Conv2D(num_filters*4, kernel_size=3, strides=1, padding='same', name=f"conv_up_{i}")(layer_input)
    u = Lambda(pixel_shuffle, name=f"pix_shuf_{i}")(u)
    return u
```
<div dir="rtl" style="text-align: justify">
    <h4>
    دلوقتي نقدر نبني الموديل:
    </h4>
</div>

```python
from tensorflow.keras.models import Model

DIV2K_RGB_MEAN = np.array([0.4488, 0.4371, 0.4040]) * 255
normalize = lambda x: (x - DIV2K_RGB_MEAN) / 127.5
denormalize = lambda x: x * 127.5 + DIV2K_RGB_MEAN

x_in = Input(shape=(None, None, 3), name="LR Batch")
x = Lambda(normalize, name="normalize_input")(x_in)
x = r = Conv2D(64, 3, padding='same', name="Conv_ip")(x)
for i in range(16):
    r = residual_block(r, 64, i)

c2 = Conv2D(64, 3, padding='same', name="conv_out")(r)
c2 = Add(name="add_out")([x, c2])
u1 = upsample_block(c2, 1)
u2 = upsample_block(u1, 2)
c3 = Conv2D(3, 3, padding='same', name="conv_final")(u2)
x_out = Lambda(denormalize, name="denormalize_output")(c3)

edsr = Model(x_in, x_out, name="EDSR")
```
<div dir="rtl" style="text-align: justify">
    <h4>
        تقدر تشوف كود الموديل بالكامل <a href="https://github.com/Ahmad-Zaki/Single_Image_Super_Resolution/blob/master/models.py#L67">هنا</a>. 
    </h4>
{{< admonition type=note title="Note" open=true >}}
لاحظ إن الموديل لا يحتوى على أي activation في الـoutput layer، وبالتالي هتحتاج تعمل clipping للـpixel values الناتجة من الموديل عشان تخلي الـrange بين 0 و 255 أو بين 0 و 1 لو عامل normalization للصور
{{< /admonition >}}
    <h4>
الـEDSR قدر يحقق نتائج أفضل من الـSRResNet وفي وقت أقل لكن ماتزال مشكلة الـsmoothing واضحة في الـoutput. عشان نحل المشكلة دى،     الـPaper الخاصة بـSRResNet أقترحت عمل fine-tuning للـweights الخاصة به عن طريق تدريبه في Generative Adversarial Network (GAN) عشان يقدر ينتج صور بتفاصيل أفضل وأكثر واقعية
    </h4>
    <h3>
        <a href="https://arxiv.org/abs/1609.04802">SRGAN</a> 
    </h3>
    <h4>
        الـPaper بتقول إن سبب الـblurring هو إن تدريب الموديل على mean square error، لإنها بتنتج output عبارة عن average لأكتر من output محتمل وبالتالي بتوصل لأقل mean square error ممكن ولكن بتسبب الـblurring الظاهر في الصورة. وبناء على كده اقترحوا طريقة جديدة لتدريب الـsuper resolution models معتمدة على GAN، وده بسبب قدرة الـGAN على توليد صور Realistic وأقرب ما تكون للصور الواقعية 
    </h4>
    <p align="center">
        <img src="/lib/img/Argument for using SRGAN.JPG" alt="GAN Effect on model output"/>
        <br>
        Fig 12: Illustration of patches from the natural image manifold (red) and super-resolved patches obtained with MSE (blue) and GAN (orange)
        <br> 
        Source: <a href="https://arxiv.org/abs/1609.04802">Click here</a> 
    </p>
    <h4>
    تدريب الموديل في GAN هيساعده في توليد تفاصيل أكثر في الصورة بفضل الـfeedback الى هياخده من الـdiscriminator، لكن بعكس تصميم الـGANs المعتاد وهو إن الـgenerator loss معتمدة على الـdiscriminator فقط، هنا لازم الـgenerator ياخد feedback مباشر عن الـoutput بتاعه، لإن انا مش مهتم فقط بإنه يطلع realistic images، ده لازم تكون الصور الناتجة محافظة عن نفس الـcontent الموجود في الصورة الأصلية، لذلك فالـgenerator loss هنا مكوّنة من جزئين:
<ol>
        <li>Adversarial loss: ده الجزء الخاص بالـfeedback الجاي من الـdiscrimator; وده بيحكم على قدرة الـgenerator على خداع الـdiscriminator ومعادلته كالآتي:</li>
        <bdo dir="ltr">
            $$
            l_{Gen} = \sum^{N}_{n=1}-log(D(I^{SR}))
            $$ 
        </bdo>
        <li>Content loss: وده الجزء الخاص بالـcontent الموجود في الصورة الأصلية، الpaper هنا بتقول إن استخدام mean square error كـcontent loss مش بيقدم نتيجة كويسة ولذلك أستخدموا VGG loss: بدل ما نعمل mean square error على الصور بشكل مباشر، هندخل الصور على pretrained VGG19 network عشان تستخرج high level features ونحسب عليكم الـmean square error. معادلة الـVGG loss موضّحة كالآتي:
        <bdo dir="ltr">
            $$
            l_{VGG/i,j} = \frac{1}{r^{2}W_{i,j}H_{i,j}}\sum^{rW_{i,j}}_{x=1}\sum^{rH_{i,j}}_{y=1}(\phi_{i,j}(I^{HR})_{x,y} - \phi_{i,j}(I^{SR})_{x,y})^2
            $$ 
        </bdo>
        حيث أن <bdo dir="ltr">$\phi_{i,j}$</bdo> تدل على الـfeature map الناتجة من الـconvolution رقم j وقبل الـmaxpooling رقم i في VGG19 network
        </li>
    </ol>
    </h4>
    <h4>
    الـgenerator loss (وتسمى هنا perceptual loss) هتكون عبارة عن weighted sum بين الـadversarial loss والـcontent loss:
    <bdo dir="ltr">
        $$
        l^{SR} = l_{VGG} + 10^{-3}l_{Gen}
        $$ 
    </bdo>
    بالنسبة للـdiscriminator المستخدم في الـGAN، فهو عبارة عن feed-forward network مهمتها هى تحديد إذا ما كان الـinput عبارة عن صورة حقيقة أو ناتجة من الـgenerator. وتصميمه موضح في Figure 13
    </h4>
    <p align="center">
        <img src="/lib/img/SRGAN Disrcriminator.JPG" alt="SRGAN Disrcriminator"/>
        <br>
        Fig 13: SRGAN Disrcriminator as described in paper
        <br> 
        Source: <a href="https://arxiv.org/abs/1609.04802">Click here</a> 
    </p>
والـloss function الخاصة به هي:
    <bdo dir="ltr">
        $$
        l_{Disc} = -\sum^{N}_{n=1}[log(D(I^{HR})) + log(1 - D(I^{SR}))]
        $$ 
    </bdo>
    بالنسبة للكود، فنقدر نبني الـdiscriminator كالآتي:
    </h4>
</div>

```python
from tensorflow.keras.layers import Input, BatchNormalization, Dense, Conv2D, LeakyReLU

def disc_block(input, no_kernels, strides) :
    """discriminator block described in paper"""
    c = Conv2D(no_kernels, kernel_size=3, strides=strides, padding='same')(input)
    c = LeakyReLU(alpha=0.2)(c)
    c = BatchNormalization(momentum=0.8)(c)
    return c
```

```python
from tensorflow.keras.models import Model

x_in = Input(shape=(None, None, 3))
c = Conv2D(64, kernel_size=3, strides=1, padding='same')(x_in)
c = LeakyReLU(alpha=0.2)(c)

d1 = disc_block(c , no_kernels=64 , strides=2)
d2 = disc_block(d1, no_kernels=128, strides=1)
d3 = disc_block(d2, no_kernels=128, strides=2)
d4 = disc_block(d3, no_kernels=256, strides=1)
d5 = disc_block(d4, no_kernels=256, strides=2)
d6 = disc_block(d5, no_kernels=512, strides=1)
d7 = disc_block(d6, no_kernels=512, strides=2)

dense1 = Dense(1024)(d7)
dense1 = LeakyReLU(alpha=0.2)(dense1)
dense2 = Dense(1, activation='sigmoid')(dense1)

discriminator =  Model(x_in, dense2, name="SRGAN_Discriminator")
```

<div dir="rtl" style="text-align: justify">
    <h4>
        تقدر تشوف كود الـdiscriminator بالكامل <a href="https://github.com/Ahmad-Zaki/Single_Image_Super_Resolution/blob/master/models.py#L125">هنا</a>. 
    </h4>
{{< admonition type=note title="Note" open=true >}}
لاحظ إن مفيش flatten layer ما بين الـdense layer والـconv layer وده معناه إن الـdiscriminator هيطلع feature map، الـfeature map دى عبارة عن أرقام بين 0 و 1 وكل واحدة فيهم هتعبر عن الـscore الخاص بجزء معين من الصورة، كده الـgenerator بدل ما ياخد score واحد بس لكل صورة بيطلعها، هياخد score مستقل لكل جزء في الصورة وبالتالي يقدر يتعلم بشكل أفضل.
{{< /admonition >}}
    <h3>
        الـdataset المستخدمة في الـtraining
    </h3>
    <h4>
    الميزة في الـsuper resolution هو إننا نقدر نكوّن dataset بشكل سهل ومباشر، ممكن أجمّع صور high resolution من أي مكان وأطبّق عليها down sampling عشان اطلع منهم صور low resolution، الـPaper الخاصة بـSRResNet و SRGAN عملت كده وطبّقت bicubic downsampling على جزء من ImageNet dataset عشان تطلع منها صور low resolution.
    </h4>
    <h4>
        لكن الـpaper الخاصة بـEDSR استخدمت dataset مختلفة وهي <a href="https://data.vision.ee.ethz.ch/cvl/DIV2K/">DIV2K</a>  ، الـdataset دى مجهزة ومصممة خصيصاً لمشكلة الـsuper resolution، تحتوي على 1000 صورة بـresolution 2k مقسّمة إلى 800 صورة training، و100 صورة validation، و100 صورة testing. الـdataset موجود منها أكتر من نسخة بناء على الـscaling factor والـdownsampling الى هتختاره، لذلك إحنا هنشتغل عليها و هنختار scaling factor= 4 و bicubic downsampling.
    </h4>
    <p align="center">
        <img src="/lib/img/div2k validation subset.JPG" alt="div2k validation subset"/>
        <br>
        Fig 14: DIV2K validation set
        <br> 
        Source: <a href="http://www.vision.ee.ethz.ch/~timofter/publications/Agustsson-CVPRW-2017.pdf">Click here</a> 
    </p>
    <h4>
        ممكن تشوف السكربت الى بيحمل الـdataset <a href="https://github.com/Ahmad-Zaki/Single_Image_Super_Resolution/blob/master/data_downloader.py">هنا</a>، أما بالنسبة للـpreprocessing فكل الى عملناه هو إننا قسمنا كل صورة high resolution لمجموعة صور بحجم 256×256 وكل صورة low resolution المناظرة لمجموعة صور بحجم 64×64 عشان يكون الـscaling factor 4 زى ما حددناه، ممكن تشوف سكربت الـpreprocessing <a href="https://github.com/Ahmad-Zaki/Single_Image_Super_Resolution/blob/master/preprocessing.py">هنا</a>. إجمالي عدد الصور بعد الـpreprocessing تخطّى 28,000 صورة وهو عدد كافي لللـtraining
    </h4>
    <h3>
        Model training
    </h3>
    <h4>
        قبل ما نعمل training لازم نعمل data loader الأول بيقرأ كل batch من الـdisc، لإن حجم الdataset ممكن يكون كبير على الـRAM، الـdata loader هيكون عبارة عن class بتـinherit من tensorflow.keras.utils.Sequence، هنعرف الـconstructor بتاعها كالآتي:
    </h4>
</div>

```python
import cv2
import numpy as np
from glob import glob
from tensorflow.keras.utils import Sequence

class Div2kLoader(Sequence):
    def __init__(self, path: str, batch_size: int = 32, shuffle: bool = True, load_all_data: bool = False):
        self.batch_size = batch_size   
        self.shuffle = shuffle
        self.load_all_data = load_all_data

        self.train_hr_paths = sorted(glob(f"{path}/HR/*"))
        self.train_lr_paths = sorted(glob(f"{path}/LR/*"))
        self.indexes = np.arange(len(self.train_hr_paths))

        self.batch_no = 0 #For the load_patch method.

        if self.load_all_data:
            self.hr_images = np.array([cv2.imread(path) for path in self.train_hr_paths])
            self.lr_images = np.array([cv2.imread(path) for path in self.train_lr_paths])
```

<div dir="rtl" style="text-align: justify">
    <h4>
        لاحظ إن الـconstructor بياخد الـpath الموجود فيه الـdata، وهنا هو بيفترض إن الـdata متقسمة لفولدرين في الـpath ده: HR بيتحوى الصور الـhigh resolution، وLR بيحتوي الصور الـlow resolution، كمان بيفترض إن كل صورة high resolution والمناظر لها في الـlow resolution لهم نفس الاسم أو على الأقل موجودين بنفس الترتيب في الفولدرين، لذلك لازم تتأكد من كده عشان الداتا تدخل بشكل صحيح للموديل.
    </h4>
    <h4>
باقي الـarguments الى في الـconstuctor استخدامها واضح من اسمها: الـbatch_size هو عدد الصور في كل batch، و shuffle عشان لو عايز أعمل shuffle للداتا بعد كل epoch، و load_all_data لو عايز أحط الداتا كلها في الـRAM لو عندي RAM تكفيها
    </h4>
</div>

```python
    def __len__(self):
        """Denotes the number of batches per epoch"""
        return int(np.floor(len(self.indexes)/self.batch_size))
    
    def on_epoch_end(self):
        """Determine what happens after each epoch"""
        #Shuffle indexes after each epoch
        self.indexes = np.arange(len(self.train_hr_paths))
        if self.shuffle:
            np.random.shuffle(self.indexes)
```

<div dir="rtl" style="text-align: justify">
    <h4>
    __len__ بنعرّفها عشان نحدد عدد الـbatches per epoch، وهو عدد الصور الكلي على الـbatch_size.
    </h4>
    <h4>
        on_epoch_end بنعرّف فيها الى احنا عايزينه يحصل بعد كل epoch، هنا هنعمل shuffle للـdata بعد كل epoch لو كنا محددين إن shuffle = True في الـ__init__.
    </h4>
</div>

```python
    def __getitem__(self, index):
      """Generates one patch of data"""
      indices = self.indexes[index * self.batch_size : (index + 1) * self.batch_size]

      if self.load_all_data:
        hr_images = self.hr_images[indices]
        lr_images = self.lr_images[indices]
      else:         
        hr_images = np.array([cv2.imread(self.train_hr_paths[i]) for i in indices])
        lr_images = np.array([cv2.imread(self.train_lr_paths[i]) for i in indices])
      return lr_images, hr_images
  
    def load_batch(self):
        """Used for training SRGAN"""
        if self.batch_no - 1 > self.__len__():
            self.on_epoch_end()
            self.batch_no = 0
        lr_images, hr_images = self.__getitem__(self.batch_no)
        self.batch_no += 1
        return lr_images, hr_images
```

<div dir="rtl" style="text-align: justify">
    <h4>
    __getitem__ هى أهم method في الـdata loader، وفيها بنقرأ الداتا من الـdisk (أو من الـRAM) ونرجعها للموديل، وفي الـmethod دى أحنا ممكن نعمل أي preprocessing إضافي على الداتا بعد ما نقرأها وقبل ما نرجعها للموديل، لكن لازم تاخد في الاعتبار إن ده هيأثر على الـtraining time بشكل كبير لو الـpreprocessing الى بتعمله تقيل، هنا احنا مش بنعمل preprocessing وبنرجع الـdata مباشرة للموديل
    </h4>
    <h4>
        load_patch مش method أساسية في الـdata loader لكننا هنعرّفها عشان نستخدمها واحنا بنعمل training للـGAN وهنشوف استخدامها في الـtraining.
    </h4>
    <h4>
        ممكن تشوف كود الـdata_loader بالكامل <a href="https://github.com/Ahmad-Zaki/Single_Image_Super_Resolution/blob/master/data_loader.py">هنا</a>
    </h4>
    <h3>
        بعد ما جهزنا الـdataset والـloader الخاص بها الأن ممكن نعمل الـtraining cycle.
    </h3>
    <h4>
        في البداية هنعمل SrganTrainer class ونجهز كل مستلزمات الـtraining:
    </h4>
</div>

```python
from data_loader import Div2kLoader
from models import edsr, srgan_discriminator
from tensorflow.keras.models import Model
from tensorflow.keras.optimizers import Adam
from tensorflow.keras.applications.vgg19 import VGG19, preprocess_input
from tensorflow.keras.callbacks import ModelCheckpoint
from tensorflow.keras.losses import MeanSquaredError, BinaryCrossentropy
import tensorflow as tf

class SrganTrainer():
    def __init__(self, generator: Model, discriminator: Model, data_path: str, lrw: int = 64, lrh: int = 64, load_all_data: bool = False, learning_rate: float = 1e-4):
        self.channels = 3
        self.lr_height = lrh
        self.lr_width = lrw
        self.lr_shape = (self.lr_height, self.lr_width, self.channels)

        self.hr_height = self.lr_height*4
        self.hr_width = self.lr_width*4
        self.hr_shape = (self.hr_height, self.hr_width, self.channels)

        self.generator = generator
        self.gen_optimizer = Adam(learning_rate)

        self.discriminator = discriminator
        self.disc_optimizer = Adam(learning_rate)

        self.data = Div2kLoader(data_path, load_all_data=load_all_data)
        self.learning_rate = learning_rate
        
        # Build the VGG network used in calculating the content Loss
        self.vgg = self._vgg_54_model()
        self._mean_squared_error = MeanSquaredError()
        self._binary_cross_entropy = BinaryCrossentropy(from_logits=False)    
```
<div dir="rtl" style="text-align: justify">
    <h4>
    في الـconstructor هنعرّف الـlow resolution shape والـhigh resolution shape، هنديله الـgenerator والـdiscriminator وهنعرّف optimizer خاص بكل موديل، الـlearning rate المستخدم في الـpaper هو 1e-4 لأول 100,000 minibatch وبعد كده بيقل لـ1e-5، لكن الى استخدمته هنا هو learning rate ثابت فقط.
    </h4>
    <h4>
    هنعرّف الـdata loader ونديله الـpath الموجود فيه الداتا، وأخيرا هنعرف بعد الـvariables الى هنستخدمهم في حسابات الـloss لكل موديل، أول حاجة هنعرفها هي الـVGG network الى هنستخدمها في حساب الـcontent loss:
    </h4>
</div>

```python
    def _vgg_54_model(self):
        """Creates a VGG model used in calculating the content loss.
        Uses the 4th convolution before the 5th pooling layer as an output layer."""
        vgg = VGG19(input_shape=self.hr_shape, include_top=False)
        vgg = Model(vgg.input, vgg.layers[20].output)
        return vgg
```

<div dir="rtl" style="text-align: justify">
    <h4>
        هنسخرج feature maps من الـconv layer رقم 4 قبل الpooling layer رقم 5 من VGG19، وهى دى الى هنحسب بيها الـcontent loss:
    </h4>
</div>

```python
    @tf.function
    def _content_loss(self, original, generated):
        generated_preprocessed = preprocess_input(generated)
        original_preprocessed = preprocess_input(original)

        sr_features = self.vgg(generated_preprocessed)/12.75
        hr_features = self.vgg(original_preprocessed)/12.75
        return self._mean_squared_error(hr_features, sr_features)
```

<div dir="rtl" style="text-align: justify">
    <h4>
        الـadversarial loss والـdiscriminator loss ممكن نحسبهم عن طريق binary cross entropy كالآتي:
    </h4>
</div>

```python
    def _adversarial_loss(self, sr_out):
         return self._binary_cross_entropy(tf.ones_like(sr_out), sr_out)

    def _discriminator_loss(self, hr_out, sr_out):
        hr_loss = self._binary_cross_entropy(tf.ones_like(hr_out), hr_out)
        sr_loss = self._binary_cross_entropy(tf.zeros_like(sr_out), sr_out)
        return hr_loss + sr_loss
```

<div dir="rtl" style="text-align: justify">
    <h4>
        الخطوة التالية هي تدريب الموديل بشكل مستقل (سواء كان SRResNet أو EDSR) الـEDSR بيحتاج وقت أقل في الـtraining والـevaluation بتاعه أفضل لذلك هنسخدمه مكان الـSRResNet.
    </h4>
</div>

```python
def train_generator(self, epochs: int = 150, starting_weights: str = None, batch_size: int = 32, loss: str = "mae"):
        if starting_weights:
            print(f"Initializing '{self.generator.name}' with {starting_weights}")
            self.generator.load_weights(starting_weights)
            
        optimizer = Adam(self.learning_rate, 0.9)
        self.generator.compile(loss= [loss], 
                               optimizer=optimizer)
        
        self.data.batch_size = batch_size
        weights_path = f"model_weights/generator_mse/{self.generator.name}_X4_MSE-{{epoch:02d}}.h5"
        checkpoint = ModelCheckpoint(weights_path,  
                                     save_best_only=True)
        
        print("Training the Generator on its own:")
        self.generator.fit(self.data, 
                           epochs=epochs, 
                           callbacks=[checkpoint])

        with open("model_json/{self.generator.name}_X4_MSE.json", "w") as json_file:
            json_file.write(self.generator.to_json())

        print(f"training '{self.generator.name}' model completed Successfully!")
        return weights_path
```

<div dir="rtl" style="text-align: justify">
    <h4>
        لاحظ إن لازم الـgenerator يكون trained لوحده باستخدام MSE أو MAE الأول قبل ما يتم تدريبه في الـGAN، السبب هو إن مهمة الـgenerator تعتبر أصعب بكتير من الـdiscriminator لإنه بيحاول يكوّن صورة كاملة، على عكس الـdiscriminator الى بيحاول يدي لكل صورة score بين 0 و 1 فقط، فلو لو جربت استخدم untrained generator هلاقي إن الـdiscriminator بيتعلم أسرع منه بكتير والgenerator مش هيقدر يتعلم كويس، لذلك خطوة الـpre-training مهمة قبل الـGAN
    </h4>
    <h4>
        عشان نعمل training للـGAN هنعمل forward pass، هنحسب الـlosses ، وأخيراً هنعمل backward pass ونحسب الـgradients ونعمل update للـweights كالآتي:
    </h4>
</div>

```python
    @tf.function
    def _train_step(self, lr, hr):
        """SRGAN training step.
        
        Takes an LR and an HR image batch as input and returns
        the computed perceptual loss and discriminator loss.
        """
        with tf.GradientTape() as gen_tape, tf.GradientTape() as disc_tape:
            lr = tf.cast(lr, tf.float32)
            hr = tf.cast(hr, tf.float32)

            # Forward pass
            sr = self.generator(lr, training=True)
            hr_output = self.discriminator(hr, training=True)
            sr_output = self.discriminator(sr, training=True)

            # Compute losses
            con_loss = self._content_loss(hr, sr)
            gen_loss = self._adversarial_loss(sr_output)
            perc_loss = con_loss + 0.001 * gen_loss
            disc_loss = self._discriminator_loss(hr_output, sr_output)


        # Compute gradient of perceptual loss w.r.t. generator weights 
        gradients_of_generator = gen_tape.gradient(perc_loss, self.generator.trainable_variables)
        # Compute gradient of discriminator loss w.r.t. discriminator weights 
        gradients_of_discriminator = disc_tape.gradient(disc_loss, self.discriminator.trainable_variables)

        # Update weights of generator and discriminator
        self.gen_optimizer.apply_gradients(zip(gradients_of_generator, generator.trainable_variables))
        self.disc_optimizer.apply_gradients(zip(gradients_of_discriminator, discriminator.trainable_variables))

        return perc_loss, disc_loss        
```

<div dir="rtl" style="text-align: justify">
    <h4>
        آخر حاجة هى إننا نكرّر الخطوة دى عدد معيّن من الـsteps لحد ما نوصل لنتيجة جيّدة، الـpaper عملت training لـ200,000 step لكن ممكن توقف الـtraining قبلها لو وصلت لنتيجة جيدة:
    </h4>
</div>

```python
    def train_gan(self, weights_path: str, steps: int = 2e5, batch_size: int = 16):
        # Prepare log file:
        with open('training_history/losses.csv', 'w') as f:
          f.write("step, perc_loss, disc_loss\n")
        
        # Initialize the generator:
        self.generator.load_weights(weights_path)

        # Specify the batch size:
        self.data.batch_size = batch_size

        for step in range(1, steps + 1):
          lr, hr = self.data.load_batch()
          pl, dl = self._train_step(lr, hr)

          print(f"Step #{step}:\n    Generator loss     = {pl}\n    Discriminator loss = {dl}\n")

          #Record losses in a csv log file:
          with open('training_history/losses.csv', 'a') as f:
            f.write(f"{step}, {pl}, {dl}\n")

          #Save Weights every 200 steps
          if step % 200 == 0:
            discriminator.save_weights( f"model_weights/disc/{discriminator.name}_X4_SRGAN.h5")
            generator.save_weights(f"model_weights/gen/{generator.name}_X4_SRGAN-{step}.h5")
            print("#############\nWeights Saved\n#############\n")    
```

<div dir="rtl" style="text-align: justify">
    <h4>
        ممكن تشوف كود الـtraining بالكامل <a href="https://github.com/Ahmad-Zaki/Single_Image_Super_Resolution/blob/master/model_trainer.py">هنا</a>
    </h4>
    <h4>
         وآخيرا آخر خطوة هى إننا نبدأ الـtraining:
    </h4>
</div>

```python
if __name__ == '__main__':
    data_path = r"datasets/preprocessed_data/"
    generator = edsr()
    discriminator = srgan_discriminator()
    gan = SrganTrainer(generator,
                       discriminator,
                       data_path=data_path,
                       load_all_data=False)

    weights_path = gan.trainGenerator(epochs=150,
                                      batch_size=32)

    gan.train_gan(weights_path,
                  steps=2e5,
                  batch_size=16) 
```
<div dir="rtl" style="text-align: justify">
    <h4>
النتيجة النهائية للـEDSR بعد تدريبه داخل الـSRGAN موضّحة في Figure 15، لاحظ التفاصيل الإضافية الموجودة في الناتج، مع اختفاء الـblurring الواضح الى كان بيظهر في ناتج الموديل السابق.
    </h4>
    <p align="center">
        <img src="/lib/img/srgan output.jpg" alt="srgan output"/>
        <br>
        Fig 15: SRGAN Result (middle), compared to input (left) and original image (right)
    </p>
    <h3>
مقارنة بين نتائج الـmodels
    </h3>
    <h4>
        الـmetrics المستخدمة في تقييم أي super resolution model هما <a href="https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio">الـPSNR</a> و<a href="https://en.wikipedia.org/wiki/Structural_similarity">الـSSIM</a>، لكن زى وضحنا سابقاً فالـPSNR الأكبر لا يعني جودة صورة أفضل، ولذلك الـpaper اعتمدت على metric جديد وهو الـmean opinion score (MOS)، وهو معتمد على تقييم الناس لنتيجة الموديل، كل مشترك بيتعرض عليه أكتر من نسخة من كل صورة لكن بدون اخباره بالموديل المستخدم في تكوينها وكل الى عليه هو اعطاء كل صورة درجة من 5.
    </h4>
    <h4>
        إحنا قررنا نعمل نفس التجربة عشان نحسب الـMOS لكل موديل وعملنا استبيان شارك فيه 67 شخص، كل مشترك اعطى تقييم لأربع نسخ من كل صورة من 13 صورة استخدمناهم في الاستبيان باجمالى 871 تقييم لكل model، النسخ الموجودة من كل صورة كانت ناتجة من bicubic interpolation, EDSR, EDSR(SRGAN), والصورة الـoriginal. وكانت النتائج كالآتي:
    </h4>
    <p align="center">
        <img src="/lib/img/performance results.png" alt="performance results"/>
        <br>
        Table 1: Models Evaluation metrics on <a href="https://uofi.box.com/shared/static/igsnfieh4lz68l926l8xbklwsnnk8we9.zip">Set14</a> and <a href="https://uofi.box.com/shared/static/kfahv87nfe8ax910l85dksyl2q212voc.zip">Set5</a>
    </p>
    <h4>
        واضح من النتائج إن EDSR هو أفضل موديل من حيث الـPSNR والـSSIM، لكن تقيييمات المشاركين فضّلت ناتج الـSRGAN عليه لإن التفاصيل فيه أفضل وبالتالي كان أقرب من باقي الـmodels للصورة الأصلية. في الـfigures التالية هستعرض نواتج كل model جنب بعض للمقارنة:
    </h4>
    <p align="center">
        <img src="/lib/img/SISR output 1.JPG" alt="SISR output"/>
        <br>
        Fig 16: Result on Painting image from Set14
        <br>
        <a href="https://drive.google.com/file/d/1sScoDhD3iW4peVUu7DxgNEuNsleErvf-/view?usp=sharing">View full image</a>
    </p>
    <br>
    <p align="center">
        <img src="/lib/img/SISR output 2.JPG" alt="SISR output"/>
        <br>
        Fig 17: Result on Zebra image from Set14
        <br>
        <a href="https://drive.google.com/file/d/1FVVhnDMdWIzLMbHHim-tE6EwCMIKVuiR/view?usp=sharing">View full image</a>
    </p>
    <br>
    <p align="center">
        <img src="/lib/img/SISR output 3.JPG" alt="SISR output"/>
        <br>
        Fig 18: Result on image 0826 from DIV2K 
        <br>
        <a href="https://drive.google.com/file/d/1YF9uyY48_6HOMh1ezpt_WeeaPpGU_ddb/view?usp=sharing">View full image</a>
    </p>
    <br>
    <p align="center">
        <img src="/lib/img/SISR output 4.JPG" alt="SISR output"/>
        <br>
        Fig 19: Result on image 0808 from DIV2K 
        <br>
        <a href="https://drive.google.com/file/d/1SFiBlx2ow-jxjeDMsmVUrXCv1BZOuAIB/view?usp=sharing">View full image</a>
    </p>
</div>

<br>
<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">

<div dir="rtl" style="text-align: justify">
    <b>
    لمزيد من المعلومات:
    </b>
</div>

* [Deep Learning for Single Image Super-Resolution: A Brief Review](https://arxiv.org/abs/1808.03344)
* [Image Super-Resolution Using Deep Convolutional Networks](https://arxiv.org/abs/1501.00092)
* [Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial Network](https://arxiv.org/abs/1609.04802)
* [Enhanced Deep Residual Networks for Single Image Super-Resolution](https://arxiv.org/abs/1707.02921)
* [Super-Resolution.Benckmark](https://github.com/huangzehao/Super-Resolution.Benckmark)
* [DIV2K dataset: DIVerse 2K resolution high quality images](https://data.vision.ee.ethz.ch/cvl/DIV2K/)
* [Peak signal-to-noise ratio (PSNR)](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio)
* [Structural Similarity Index Measure (SSIM)](https://en.wikipedia.org/wiki/Structural_similarity)
<br>

<hr style="height:0px;border:none;color:#333;border-top: 1px solid black">
