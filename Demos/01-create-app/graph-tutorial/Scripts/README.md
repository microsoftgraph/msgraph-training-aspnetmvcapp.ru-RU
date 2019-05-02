<!-- IGNORE THE HTML BLOCK BELOW, THE INTERESTING PART IS AFTER IT -->

<h1 align="center"><span data-ttu-id="de8cd-101">Поппер. js</span><span class="sxs-lookup"><span data-stu-id="de8cd-101">Popper.js</span></span></h1>

<p align="center"><span data-ttu-id="de8cd-102">
    <strong>Библиотека, используемая для размещения выталкивания в веб-приложениях.</strong>
</span><span class="sxs-lookup"><span data-stu-id="de8cd-102">
    <strong>A library used to position poppers in web applications.</strong>
</span></span></p>

<p align="center">
    <a href="https://travis-ci.org/FezVrasta/popper.js/branches" target="_blank"><img src="https://travis-ci.org/FezVrasta/popper.js.svg?branch=master" alt="Build Status"/></a>
    <img src="http://img.badgesize.io/https://unpkg.com/popper.js/dist/popper.min.js?compression=gzip" alt="Stable Release Size"/>
    <a href="https://www.bithound.io/github/FezVrasta/popper.js"><img src="https://www.bithound.io/github/FezVrasta/popper.js/badges/score.svg" alt="bitHound Overall Score"></a>
    <a href="https://codeclimate.com/github/FezVrasta/popper.js/coverage"><img src="https://codeclimate.com/github/FezVrasta/popper.js/badges/coverage.svg" alt="Istanbul Code Coverage"/></a>
    <a href="https://gitter.im/FezVrasta/popper.js" target="_blank"><img src="https://img.shields.io/gitter/room/nwjs/nw.js.svg" alt="Get support or discuss"/></a>
    <br />
    <a href="https://saucelabs.com/u/popperjs" target="_blank"><img src="https://badges.herokuapp.com/browsers?labels=none&googlechrome=latest&firefox=latest&microsoftedge=latest&iexplore=11,10&safari=latest&iphone=latest" alt="SauceLabs Reports"/></a>
</p>

<img src="https://raw.githubusercontent.com/FezVrasta/popper.js/master/popperjs.png" align="right" width=250 />

<!-- 🚨 HEY! HERE BEGINS THE INTERESTING STUFF 🚨 -->

## <a name="wut-poppers"></a><span data-ttu-id="de8cd-103">ВУТ?</span><span class="sxs-lookup"><span data-stu-id="de8cd-103">Wut?</span></span> <span data-ttu-id="de8cd-104">Выталкивания?</span><span class="sxs-lookup"><span data-stu-id="de8cd-104">Poppers?</span></span>

<span data-ttu-id="de8cd-105">Поппер — это элемент на экране, который "выталкивает" из естественного процесса приложения.</span><span class="sxs-lookup"><span data-stu-id="de8cd-105">A popper is an element on the screen which "pops out" from the natural flow of your application.</span></span>  
<span data-ttu-id="de8cd-106">Распространенными примерами всплывающих подсказок являются подсказки, поповерс и раскрывающиеся окна.</span><span class="sxs-lookup"><span data-stu-id="de8cd-106">Common examples of poppers are tooltips, popovers and drop-downs.</span></span>


## <a name="so-yet-another-tooltip-library"></a><span data-ttu-id="de8cd-107">Итак, еще одна библиотека всплывающих подсказок?</span><span class="sxs-lookup"><span data-stu-id="de8cd-107">So, yet another tooltip library?</span></span>

<span data-ttu-id="de8cd-108">Ну, по сути, **нет**.</span><span class="sxs-lookup"><span data-stu-id="de8cd-108">Well, basically, **no**.</span></span>  
<span data-ttu-id="de8cd-109">Поппер. js — это **механизм позиционирования**, предназначенный для вычисления положения элемента, чтобы его можно было разместить рядом с заданном ссылочным элементом.</span><span class="sxs-lookup"><span data-stu-id="de8cd-109">Popper.js is a **positioning engine**, its purpose is to calculate the position of an element to make it possible to position it near a given reference element.</span></span>  

<span data-ttu-id="de8cd-110">Модуль полностью модульный и большинство его компонентов реализуются как модификаторы ( \*\*\*\* аналогично промежуточным или подключаемым по).</span><span class="sxs-lookup"><span data-stu-id="de8cd-110">The engine is completely modular and most of its features are implemented as **modifiers** (similar to middlewares or plugins).</span></span>  
<span data-ttu-id="de8cd-111">База кода целиком написана в ES2015, а ее компоненты автоматически тестируются на реальных браузерах с [сауцелабс](https://saucelabs.com/) и [трависЦи](https://travis-ci.org/).</span><span class="sxs-lookup"><span data-stu-id="de8cd-111">The whole code base is written in ES2015 and its features are automatically tested on real browsers thanks to [SauceLabs](https://saucelabs.com/) and [TravisCI](https://travis-ci.org/).</span></span>

<span data-ttu-id="de8cd-112">Поппер. js имеет нулевые зависимости.</span><span class="sxs-lookup"><span data-stu-id="de8cd-112">Popper.js has zero dependencies.</span></span> <span data-ttu-id="de8cd-113">Нет jQuery, без LoDash, Nothing.</span><span class="sxs-lookup"><span data-stu-id="de8cd-113">No jQuery, no LoDash, nothing.</span></span>  
<span data-ttu-id="de8cd-114">Он используется крупными компаниями, такими как [Twitter в начальной загрузке v4](https://getbootstrap.com/), [Майкрософт в веб](https://github.com/OneNoteDev/WebClipper) – [атласкит и атлассиан в](https://aui-cdn.atlassian.com/atlaskit/registry/).</span><span class="sxs-lookup"><span data-stu-id="de8cd-114">It's used by big companies like [Twitter in Bootstrap v4](https://getbootstrap.com/), [Microsoft in WebClipper](https://github.com/OneNoteDev/WebClipper) and [Atlassian in AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/).</span></span>

### <a name="popperjs"></a><span data-ttu-id="de8cd-115">Поппер. js</span><span class="sxs-lookup"><span data-stu-id="de8cd-115">Popper.js</span></span>

<span data-ttu-id="de8cd-116">Это обработчик, Библиотека, которая вычисляет и, при необходимости, применяет эти стили к средствам выталкивания.</span><span class="sxs-lookup"><span data-stu-id="de8cd-116">This is the engine, the library that computes and, optionally, applies the styles to the poppers.</span></span>

<span data-ttu-id="de8cd-117">Вот некоторые ключевые моменты:</span><span class="sxs-lookup"><span data-stu-id="de8cd-117">Some of the key points are:</span></span>

- <span data-ttu-id="de8cd-118">Располагать элементы в исходном контексте DOM (не запутаться с МОДЕЛЬЮ DOM!);</span><span class="sxs-lookup"><span data-stu-id="de8cd-118">Position elements keeping them in their original DOM context (doesn't mess with your DOM!);</span></span>
- <span data-ttu-id="de8cd-119">Позволяет экспортировать вычисляемые сведения для интеграции с другими библиотеками представлений;</span><span class="sxs-lookup"><span data-stu-id="de8cd-119">Allows to export the computed informations to integrate with React and other view libraries;</span></span>
- <span data-ttu-id="de8cd-120">Поддерживает теневые элементы DOM;</span><span class="sxs-lookup"><span data-stu-id="de8cd-120">Supports Shadow DOM elements;</span></span>
- <span data-ttu-id="de8cd-121">Полностью настраиваемая с учетом структуры модификаторов;</span><span class="sxs-lookup"><span data-stu-id="de8cd-121">Completely customizable thanks to the modifiers based structure;</span></span>

<span data-ttu-id="de8cd-122">Посетите [страницу проекта](https://fezvrasta.github.io/popper.js) , чтобы увидеть множество примеров того, что можно делать с Поппер. js!</span><span class="sxs-lookup"><span data-stu-id="de8cd-122">Visit our [project page](https://fezvrasta.github.io/popper.js) to see a lot of examples of what you can do with Popper.js!</span></span>

<span data-ttu-id="de8cd-123">[Здесь](/docs/_includes/popper-documentation.md)вы найдете документацию.</span><span class="sxs-lookup"><span data-stu-id="de8cd-123">Find [the documentation here](/docs/_includes/popper-documentation.md).</span></span>


### <a name="tooltipjs"></a><span data-ttu-id="de8cd-124">ToolTip. js</span><span class="sxs-lookup"><span data-stu-id="de8cd-124">Tooltip.js</span></span>

<span data-ttu-id="de8cd-125">Так как многим пользователям достаточно простого способа интеграции мощных всплывающих подсказок в своих проектах, мы создали **ToolTip. js**.</span><span class="sxs-lookup"><span data-stu-id="de8cd-125">Since lots of users just need a simple way to integrate powerful tooltips in their projects, we created **Tooltip.js**.</span></span>  
<span data-ttu-id="de8cd-126">Это небольшая библиотека, которая упрощает автоматическое создание всплывающих подсказок с помощью ядра Поппер. js.</span><span class="sxs-lookup"><span data-stu-id="de8cd-126">It's a small library that makes it easy to automatically create tooltips using as engine Popper.js.</span></span>  
<span data-ttu-id="de8cd-127">Его API почти идентичны с помощью знаменитой системы всплывающих подсказок начальной загрузки, таким образом, она будет легко интегрироваться в проекты.</span><span class="sxs-lookup"><span data-stu-id="de8cd-127">Its API is almost identical to the famous tooltip system of Bootstrap, in this way it will be easy to integrate it in your projects.</span></span>  
<span data-ttu-id="de8cd-128">Всплывающие подсказки, создаваемые ToolTip. js, доступны благодаря `aria` тегам.</span><span class="sxs-lookup"><span data-stu-id="de8cd-128">The tooltips generated by Tooltip.js are accessible thanks to the `aria` tags.</span></span>

<span data-ttu-id="de8cd-129">[Здесь](/docs/_includes/tooltip-documentation.md)вы найдете документацию.</span><span class="sxs-lookup"><span data-stu-id="de8cd-129">Find [the documentation here](/docs/_includes/tooltip-documentation.md).</span></span>


## <a name="installation"></a><span data-ttu-id="de8cd-130">Установка</span><span class="sxs-lookup"><span data-stu-id="de8cd-130">Installation</span></span>
<span data-ttu-id="de8cd-131">Поппер. js доступен для следующих диспетчеров пакетов и сети CDN:</span><span class="sxs-lookup"><span data-stu-id="de8cd-131">Popper.js is available on the following package managers and CDNs:</span></span>

| <span data-ttu-id="de8cd-132">Source</span><span class="sxs-lookup"><span data-stu-id="de8cd-132">Source</span></span> |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="de8cd-133">npm</span><span class="sxs-lookup"><span data-stu-id="de8cd-133">npm</span></span>    | `npm install popper.js --save`                                                   |
| <span data-ttu-id="de8cd-134">Yarn</span><span class="sxs-lookup"><span data-stu-id="de8cd-134">yarn</span></span>   | `yarn add popper.js`                                                             |
| <span data-ttu-id="de8cd-135">NuGet</span><span class="sxs-lookup"><span data-stu-id="de8cd-135">NuGet</span></span>  | `PM> Install-Package popper.js`                                                  |
| <span data-ttu-id="de8cd-136">Bower</span><span class="sxs-lookup"><span data-stu-id="de8cd-136">Bower</span></span>  | `bower install popper.js --save`                     |
| <span data-ttu-id="de8cd-137">унпкг</span><span class="sxs-lookup"><span data-stu-id="de8cd-137">unpkg</span></span>  | [`https://unpkg.com/popper.js`](https://unpkg.com/popper.js)                     |
| <span data-ttu-id="de8cd-138">кднжс</span><span class="sxs-lookup"><span data-stu-id="de8cd-138">cdnjs</span></span>  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

<span data-ttu-id="de8cd-139">Подсказка и JS:</span><span class="sxs-lookup"><span data-stu-id="de8cd-139">Tooltip.js as well:</span></span>

| <span data-ttu-id="de8cd-140">Source</span><span class="sxs-lookup"><span data-stu-id="de8cd-140">Source</span></span> |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="de8cd-141">npm</span><span class="sxs-lookup"><span data-stu-id="de8cd-141">npm</span></span>    | `npm install tooltip.js --save`                                                  |
| <span data-ttu-id="de8cd-142">Yarn</span><span class="sxs-lookup"><span data-stu-id="de8cd-142">yarn</span></span>   | `yarn add tooltip.js`                                                            |
| <span data-ttu-id="de8cd-143">Бовер \*</span><span class="sxs-lookup"><span data-stu-id="de8cd-143">Bower\*</span></span> | `bower install tooltip.js=https://unpkg.com/tooltip.js --save`                   |
| <span data-ttu-id="de8cd-144">унпкг</span><span class="sxs-lookup"><span data-stu-id="de8cd-144">unpkg</span></span>  | [`https://unpkg.com/tooltip.js`](https://unpkg.com/tooltip.js)                   |
| <span data-ttu-id="de8cd-145">кднжс</span><span class="sxs-lookup"><span data-stu-id="de8cd-145">cdnjs</span></span>  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

<span data-ttu-id="de8cd-146">\*: Бовер не поддерживается, его можно использовать для установки ToolTip. js только траугх сети CDN unpkg.com.</span><span class="sxs-lookup"><span data-stu-id="de8cd-146">\*: Bower isn't officially supported, it can be used to install Tooltip.js only trough the unpkg.com CDN.</span></span> <span data-ttu-id="de8cd-147">Этот метод имеет ограничение, недоступное для определения определенной версии библиотеки.</span><span class="sxs-lookup"><span data-stu-id="de8cd-147">This method has the limitation of not being able to define a specific version of the library.</span></span> <span data-ttu-id="de8cd-148">Бовер и Поппер. js предлагают использовать для проектов NPM или Yarn.</span><span class="sxs-lookup"><span data-stu-id="de8cd-148">Bower and Popper.js suggests to use npm or Yarn for your projects.</span></span>  
<span data-ttu-id="de8cd-149">Для получения дополнительных сведений [Ознакомьтесь со связанной ошибкой](https://github.com/FezVrasta/popper.js/issues/390).</span><span class="sxs-lookup"><span data-stu-id="de8cd-149">For more info, [read the related issue](https://github.com/FezVrasta/popper.js/issues/390).</span></span>

### <a name="dist-targets"></a><span data-ttu-id="de8cd-150">Целевые объекты Redist</span><span class="sxs-lookup"><span data-stu-id="de8cd-150">Dist targets</span></span>

<span data-ttu-id="de8cd-151">В настоящее время Поппер. js поставляется с 3 целевыми объектами: УМД, ЕСМ и Еснекст.</span><span class="sxs-lookup"><span data-stu-id="de8cd-151">Popper.js is currently shipped with 3 targets in mind: UMD, ESM and ESNext.</span></span>

- <span data-ttu-id="de8cd-152">УМД — определение универсального модуля: AMD, Рекуирежс и глобальные компоненты;</span><span class="sxs-lookup"><span data-stu-id="de8cd-152">UMD - Universal Module Definition: AMD, RequireJS and globals;</span></span>
- <span data-ttu-id="de8cd-153">ЕСМ-ES modules: for Pack/ROLLUP или браузер, поддерживающий спецификацию;</span><span class="sxs-lookup"><span data-stu-id="de8cd-153">ESM - ES Modules: For webpack/Rollup or browser supporting the spec;</span></span>
- <span data-ttu-id="de8cd-154">Еснекст: доступно в `dist/`, может использоваться с пакетом Pack и `babel-preset-env`;</span><span class="sxs-lookup"><span data-stu-id="de8cd-154">ESNext: Available in `dist/`, can be used with webpack and `babel-preset-env`;</span></span>

<span data-ttu-id="de8cd-155">Обязательно используйте нужную подправку.</span><span class="sxs-lookup"><span data-stu-id="de8cd-155">Make sure to use the right one for your needs.</span></span> <span data-ttu-id="de8cd-156">Если вы хотите импортировать его с помощью `<script>` тега, используйте УМД.</span><span class="sxs-lookup"><span data-stu-id="de8cd-156">If you want to import it with a `<script>` tag, use UMD.</span></span>

## <a name="usage"></a><span data-ttu-id="de8cd-157">Применение</span><span class="sxs-lookup"><span data-stu-id="de8cd-157">Usage</span></span>

<span data-ttu-id="de8cd-158">При наличии существующего узла DOM Поппер запросите Поппер. js, чтобы разместить его рядом с кнопкой</span><span class="sxs-lookup"><span data-stu-id="de8cd-158">Given an existing popper DOM node, ask Popper.js to position it near its button</span></span>

```js
var reference = document.querySelector('.my-button');
var popper = document.querySelector('.my-popper');
var anotherPopper = new Popper(
    reference,
    popper,
    {
        // popper options here
    }
);
```

### <a name="callbacks"></a><span data-ttu-id="de8cd-159">Обратных вызовов</span><span class="sxs-lookup"><span data-stu-id="de8cd-159">Callbacks</span></span>

<span data-ttu-id="de8cd-160">Поппер. js поддерживает два типа обратных вызовов, а `onCreate` обратный вызов вызывается после того, как Поппер инитализед.</span><span class="sxs-lookup"><span data-stu-id="de8cd-160">Popper.js supports two kinds of callbacks, the `onCreate` callback is called after the popper has been initalized.</span></span> <span data-ttu-id="de8cd-161">`onUpdate` Он вызывается при каждом последующем обновлении.</span><span class="sxs-lookup"><span data-stu-id="de8cd-161">The `onUpdate` one is called on any subsequent update.</span></span>

```js
const reference = document.querySelector('.my-button');
const popper = document.querySelector('.my-popper');
new Popper(reference, popper, {
    onCreate: (data) => {
        // data is an object containing all the informations computed
        // by Popper.js and used to style the popper and its arrow
        // The complete description is available in Popper.js documentation
    },
    onUpdate: (data) => {
        // same as `onCreate` but called on subsequent updates
    }
});
```

### <a name="writing-your-own-modifiers"></a><span data-ttu-id="de8cd-162">Написание собственных модификаторов</span><span class="sxs-lookup"><span data-stu-id="de8cd-162">Writing your own modifiers</span></span>

<span data-ttu-id="de8cd-163">Поппер. js основан на архитектуре "подключаемый модуль", большинство ее функций полностью инкапсулируются в качестве модификаторов.</span><span class="sxs-lookup"><span data-stu-id="de8cd-163">Popper.js is based on a "plugin-like" architecture, most of its features are fully encapsulated "modifiers".</span></span>  
<span data-ttu-id="de8cd-164">Модификатор — это функция, которая вызывается каждый раз, когда Поппер. js необходимо вычислить положение Поппер.</span><span class="sxs-lookup"><span data-stu-id="de8cd-164">A modifier is a function that is called each time Popper.js needs to compute the position of the popper.</span></span> <span data-ttu-id="de8cd-165">По этой причине модификаторы должны быть очень производительными, чтобы избежать узких мест.</span><span class="sxs-lookup"><span data-stu-id="de8cd-165">For this reason, modifiers should be very performant to avoid bottlenecks.</span></span>  

<span data-ttu-id="de8cd-166">Чтобы узнать, как создать модификатор, [прочитайте документацию по изменениям](docs/_includes/popper-documentation.md#modifiers--object)</span><span class="sxs-lookup"><span data-stu-id="de8cd-166">To learn how to create a modifier, [read the modifiers documentation](docs/_includes/popper-documentation.md#modifiers--object)</span></span>


### <a name="react-vuejs-angular-angularjs-emberjs-etc-integration"></a><span data-ttu-id="de8cd-167">Интеграция, Vue. js, угловая, AngularJS, Ембер. js (и т. д.)</span><span class="sxs-lookup"><span data-stu-id="de8cd-167">React, Vue.js, Angular, AngularJS, Ember.js (etc...) integration</span></span>

<span data-ttu-id="de8cd-168">Интеграция сторонних библиотек в реагирующие или в других библиотеках может быть очень недостаточной, так как они обычно изменяют модель DOM и блокируют библиотеки Crazy.</span><span class="sxs-lookup"><span data-stu-id="de8cd-168">Integrating 3rd party libraries in React or other libraries can be a pain because they usually alter the DOM and drive the libraries crazy.</span></span>  
<span data-ttu-id="de8cd-169">Поппер. js применяет все изменения DOM в `applyStyle` пределах модификатора, вы можете просто отключить его и вручную применить координаты Поппер с помощью библиотеки выбора.</span><span class="sxs-lookup"><span data-stu-id="de8cd-169">Popper.js limits all its DOM modifications inside the `applyStyle` modifier, you can simply disable it and manually apply the popper coordinates using your library of choice.</span></span>  

<span data-ttu-id="de8cd-170">Полный список библиотек, которые позволяют использовать Поппер. js в существующих платформах, можно найти на странице [упоминания](/MENTIONS.md) .</span><span class="sxs-lookup"><span data-stu-id="de8cd-170">For a comprehensive list of libraries that let you use Popper.js into existing frameworks, visit the [MENTIONS](/MENTIONS.md) page.</span></span>

<span data-ttu-id="de8cd-171">Кроме того, вы можете даже переопределить свой `applyStyles` собственный пользователь и интегрировать Поппер. js самостоятельно!</span><span class="sxs-lookup"><span data-stu-id="de8cd-171">Alternatively, you may even override your own `applyStyles` with your custom one and integrate Popper.js by yourself!</span></span>

```js
function applyReactStyle(data) {
    // export data in your framework and use its content to apply the style to your popper
};

const reference = document.querySelector('.my-button');
const popper = document.querySelector('.my-popper');
new Popper(reference, popper, {
    modifiers: {
        applyStyle: { enabled: false },
        applyReactStyle: {
            enabled: true,
            fn: applyReactStyle,
            order: 800,
        },
    },
});

```

### <a name="migration-from-popperjs-v0"></a><span data-ttu-id="de8cd-172">Миграция из Поппер. js v0</span><span class="sxs-lookup"><span data-stu-id="de8cd-172">Migration from Popper.js v0</span></span>

<span data-ttu-id="de8cd-173">Так как API изменился, мы подготовили некоторые инструкции по миграции для упрощения обновления до Поппер. js v1.</span><span class="sxs-lookup"><span data-stu-id="de8cd-173">Since the API changed, we prepared some migration instructions to make it easy to upgrade to Popper.js v1.</span></span>  

https://github.com/FezVrasta/popper.js/issues/62

<span data-ttu-id="de8cd-174">Если у вас возникнут вопросы, вы можете прокомментировать эту ошибку.</span><span class="sxs-lookup"><span data-stu-id="de8cd-174">Feel free to comment inside the issue if you have any questions.</span></span>

### <a name="performances"></a><span data-ttu-id="de8cd-175">Перформанцес</span><span class="sxs-lookup"><span data-stu-id="de8cd-175">Performances</span></span>

<span data-ttu-id="de8cd-176">Поппер. js очень производительно.</span><span class="sxs-lookup"><span data-stu-id="de8cd-176">Popper.js is very performant.</span></span> <span data-ttu-id="de8cd-177">Обычно он занимает 0,5 мс, чтобы вычислить положение Поппер (на ИМАК с Intel Core i5 с 3,5-Е ГГц).</span><span class="sxs-lookup"><span data-stu-id="de8cd-177">It usually takes 0.5ms to compute a popper's position (on an iMac with 3.5G GHz Intel Core i5).</span></span>  
<span data-ttu-id="de8cd-178">Это означает, что он не будет вызывать никаких [жанк](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank), что приводит к гладкому взаимодействию с пользователем.</span><span class="sxs-lookup"><span data-stu-id="de8cd-178">This means that it will not cause any [jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank), leading to a smooth user experience.</span></span>

## <a name="notes"></a><span data-ttu-id="de8cd-179">Примечания</span><span class="sxs-lookup"><span data-stu-id="de8cd-179">Notes</span></span>

### <a name="libraries-using-popperjs"></a><span data-ttu-id="de8cd-180">Библиотеки, использующие Поппер. js</span><span class="sxs-lookup"><span data-stu-id="de8cd-180">Libraries using Popper.js</span></span>

<span data-ttu-id="de8cd-181">Цель Поппер. js состоит в том, чтобы предоставить стабильный и мощный механизм позиционирования, готовый для использования в сторонних библиотеках.</span><span class="sxs-lookup"><span data-stu-id="de8cd-181">The aim of Popper.js is to provide a stable and powerful positioning engine ready to be used in 3rd party libraries.</span></span>  

<span data-ttu-id="de8cd-182">Посетите страницу [упоминания](/MENTIONS.md) , чтобы получить обновленный список проектов.</span><span class="sxs-lookup"><span data-stu-id="de8cd-182">Visit the [MENTIONS](/MENTIONS.md) page for an updated list of projects.</span></span>


### <a name="credits"></a><span data-ttu-id="de8cd-183">Сведения об авторах</span><span class="sxs-lookup"><span data-stu-id="de8cd-183">Credits</span></span>
<span data-ttu-id="de8cd-184">Я хочу поблагодарить некоторых друзей и проектов для работы, которая выполнялась следующим образом:</span><span class="sxs-lookup"><span data-stu-id="de8cd-184">I want to thank some friends and projects for the work they did:</span></span>

- <span data-ttu-id="de8cd-185">[@AndreaScn](https://github.com/AndreaScn) для своей работы на странице GitHub и ручного тестирования, проведенного во время разработки;</span><span class="sxs-lookup"><span data-stu-id="de8cd-185">[@AndreaScn](https://github.com/AndreaScn) for his work on the GitHub Page and the manual testing he did during the development;</span></span>
- <span data-ttu-id="de8cd-186">[@vampolo](https://github.com/vampolo) для исходной идеи и для имени библиотеки;</span><span class="sxs-lookup"><span data-stu-id="de8cd-186">[@vampolo](https://github.com/vampolo) for the original idea and for the name of the library;</span></span>
- <span data-ttu-id="de8cd-187">[Сисдиг](https://github.com/Draios) все функции, полученные в течение этих лет, которые делают возможным написание этой библиотеки;</span><span class="sxs-lookup"><span data-stu-id="de8cd-187">[Sysdig](https://github.com/Draios) for all the awesome things I learned during these years that made it possible for me to write this library;</span></span>
- <span data-ttu-id="de8cd-188">Для [модема. js](http://github.hubspot.com/tether/) существуют проблемы с написанием библиотеки размещений, готовой для реального мира;</span><span class="sxs-lookup"><span data-stu-id="de8cd-188">[Tether.js](http://github.hubspot.com/tether/) for having inspired me in writing a positioning library ready for the real world;</span></span>
- <span data-ttu-id="de8cd-189">[Участники](https://github.com/FezVrasta/popper.js/graphs/contributors) для получения очень оцененных запросов на включение внесенных изменений и отчетов об ошибках;</span><span class="sxs-lookup"><span data-stu-id="de8cd-189">[The Contributors](https://github.com/FezVrasta/popper.js/graphs/contributors) for their much appreciated Pull Requests and bug reports;</span></span>
- <span data-ttu-id="de8cd-190">**вы** хотите присвоить этому проекту звезду, чтобы сделать проект недопустимым.🙂</span><span class="sxs-lookup"><span data-stu-id="de8cd-190">**you** for the star you'll give to this project and for being so awesome to give this project a try 🙂</span></span>

### <a name="copyright-and-license"></a><span data-ttu-id="de8cd-191">Авторские права и лицензия</span><span class="sxs-lookup"><span data-stu-id="de8cd-191">Copyright and license</span></span>
<span data-ttu-id="de8cd-192">Авторские права на код и документация 2016 **Федерико зиволо**.</span><span class="sxs-lookup"><span data-stu-id="de8cd-192">Code and documentation copyright 2016 **Federico Zivolo**.</span></span> <span data-ttu-id="de8cd-193">Код, выпущенный под [лицензией MIT](LICENSE.md).</span><span class="sxs-lookup"><span data-stu-id="de8cd-193">Code released under the [MIT license](LICENSE.md).</span></span> <span data-ttu-id="de8cd-194">Документы, выпущенные в Creative Commons.</span><span class="sxs-lookup"><span data-stu-id="de8cd-194">Docs released under Creative Commons.</span></span>
