# Angular4

## 模板语法
* 基本插值语法 {{ value }}
* 模板内的局部变量
* 单向值绑定
* 事件绑定
* 双向绑定
* *ngIf的用法
* *ngFor用法
* NgClass用法
* NgStyle用法
* NgModel的用法
* 管道的用法
    + 大小写转换管道
        - uppercase将字符串转换为大写
        - lowercase将字符串转换为小写
    + 日期管道
        - {{today | date:'yyyy-MM-dd HH:mm:ss'}}
    + 小数管道
        - {{pi | number:'2.2-4'}}
    + 货币管道
        - {{a | currency:'USD':false}}
    + 自定义管道 @Pipe
        实现接口PipeTransform
* 安全取值 {{currentRace?.name}}

## Angular 元数据 (Metadata) (元数据告诉 Angular 如何处理一个类)
* @Component装饰器能接受一个配置对象， Angular 会基于这些信息创建和展示组件及其视图
+ selector	string	自定义组件的标签，用于匹配元素。
+ inputs	string[]	指定组件的输入属性。
+ outputs	string[]	指定组件的输出属性
+ host	{[key:string],string}	指定指令或组件的事件，动作和属性等
+ providers	any[]	指定该组件及其所有子组件（含ContentChildren）可用的服务依赖注入
+ exportAs	string	给指令分配一个变量，使其可以在模板中使用
+ moduleId	string	包含该组件模块的id，他被用于解析模板和样式的相对路径
+ queries	{[key:string],any}	设置需要被注入到组件的查询
+ viewProviders	any[]	指定该组件及其所有子组件可用的服务
+ changeDetection	ChangeDetectionStrategy	指定使用的变化监测策略
+ templateUrl	string	模板路径
+ styleUrls	string[]	样式路径
+ template	string	内联模板
+ styles	string[]	内联样式
+ animations	AnimationEntryMetadata[]	angular动画
+ encapsulation	ViewEncapsulation	设置组件的试图包装选项
+ interpolation	[string,string]	设置自定义插值标记，默认是{{}}

## EmbeddedViewRef 
(class EmbeddedViewRef extends ViewRef)
    代表一个angular视图。一个view是应用程序UI的基本构建块。它是可以一起创建和销毁的最小的元素组合。
    一个视图中元素的属性可以更改，但View中的元素的结构（数量和顺序）不能。
    更改Elements的结构只能通过ViewContainerRef插入，移动或删除嵌套视图来完成。
    每个视图都可以包含许多视图容器。

## ViewRef (class ViewRef extends ChangeDetectorRef)

## ElementRef
在应用层直接操作 DOM，就会造成应用层与渲染层之间强耦合，导致我们的应用无法运行在不同环境，如 web worker 中，因为在 web worker 环境中，是不能直接操作 DOM。有兴趣的读者，可以阅读一下 Web Workers 中支持的类和方法这篇文章。通过 ElementRef 我们就可以封装不同平台下视图层中的 native 元素 (在浏览器环境中，native 元素通常是指 DOM 元素)，最后借助于 Angular 提供的强大的依赖注入特性，我们就可以轻松地访问到 native 元素。

* 1.引入相关api

    import {Component, OnInit, Input, Output, EventEmitter, AfterViewInit, ElementRef, ViewChild, Renderer2} from '@angular/core';
    
* 2.构造函数依赖注入

    constructor( 
        private elementRef: ElementRef,
        private renderer: Renderer2) {
    }
    
* 3.使用属性装饰符@ViewChild

    <div class="contents" [innerHTML]="content | translate" #divContent></div>
    
* 4.设置相关样式

    ngAfterViewInit() {
        this.renderer.setStyle(this.greetDiv.nativeElement, 'width', '3.60rem');
    }

## 生命周期

* ngOnChanges()

    用处：当Angular设置数据绑定输入属性发生变化时响应。
    
    时机：当被绑定的输入属性的值发生变化时调用，不过首次调用是会发生在ngOnInit()之前的。
    
    ngOnChanges()方法获取了一个对象，它可以同时观测1个/多个绑定的属性值，它把每个发生变化的属性名都映射到了一个SimpleChange对象， 该对象中有属性的当前值和前一个值。
    
* ngOnInit()

用处：

    在Angular第一次显示数据绑定和设置指令/组件的输入属性之后，初始化指令/组件。
    
时机：

    在第一轮ngOnChanges()完成之后调用，只调用一次。
    
    要明确一点就是，虽然接口中有一个constructor构造函数，但是在这里最好只对局部变量进行初始化之外什么都不做，通过ngOnInit()获取初始数据，而且是放在构造函数后面。
    
* ngDoCheck()
    用处：检测那些Angular自身无法捕获的变更
    时机：在每个Angular变更检测周期中调用，ngOnChanges()和ngOnInit()之后。
    谨慎使用，因为一些你意想不到的事情也会被视为变更了
    
* ngAfterContentInit()和ngAfterContentChecked() (只适用于组件)
    用处：在外来内容被投影到组件中之后/投影组件内容的变更检测之后调用
    时机：
    ngAfterContentInit()--第一次ngDoCheck()之后调用，且只调用一次；
    ngAfterContentChecked()--每次ngDoCheck()之后调用，如果需要调用ngAfterContentInit()，则在ngAfterContentInit()调用之后
    
* ngAfterViewInit()和ngAfterViewChecked() (只适用于组件)
    用处：初始化完/检测变更完组件视图及其子视图之后调用。
    时机：跟相应的content钩子类似，在对应的content钩子后面。
    
* ngOnDestroy
    用处：反订阅可观察对象和分离事件处理器，以防内存泄漏
    时机：销毁指令/组件之前调用并清扫

## @HostListener()
HostListener是属性装饰器，用来为宿主元素添加事件监听。
定义：
    
    // HostListenerDecorator的定义
    export interface HostListenerDecorator {
        (eventName: string, args?: string[]): any;
        new (eventName: string, args?: string[]): any;
    }
    
应用：
    
    import {HostListener} from '@angular/core';

    @HostListener('click', ['$event.target'])
    onClick(btn: HTMLElement) {
        console.log('button', btn, 'number of clicks:', this.numberOfClicks++);
    }

## @HostBinding

HostBinding 是属性装饰器，用来动态设置宿主元素的属性值。
定义：
    
    export interface HostBindingDecorator {
        (hostPropertyName?: string): any;
        new (hostPropertyName?: string): any;
    }
    
应用：
    
    import { Directive, HostBinding, HostListener } from '@angular/core';
    @HostBindings('attr.foo') foo = 'bar'

## 父子组件间通信

    @Input() 和 @Output() 允许 Angular 在其父上下文和子指令或组件之间共享数据。
    @Input() 属性是可写的，而 @Output() 属性是可观察对象。

## service 服务注入

## FormControl
    FormControl代表单一的输入字段，它是Angular表单中的最小单元。
    FormControl封装了这些字段的值和状态，比如是否有效、是否脏（被修改过）或是否有错误等。

列：

    <input type="text" [formControl]="name" /> 
    let nameControl = new FormControl("Nate"); 
    let name = nameControl.value; // -> Nate
    nameControl.errors // -> StringMap<string, any> of errors 
    nameControl.dirty  // -> false 
    nameControl.valid  // -> true

## FormGroup

    大多数表单都拥有不止一个字段，因此我们需要某种方式来管理多个FormControl。假设我们要检查表单的有效性。如果要遍历这个FormControl数组并检查每一个FormControl是否有效，必然相当繁琐；而FormGroup则可以为一组FormControl提供总包接口（wrapper interface），来解决这种问题。
    下面是FormGroup的创建方式：

    let personInfo = new FormGroup({ 
        firstName: new FormControl("Nate"), 
        lastName: new FormControl("Murray"), 
        zip: new FormControl("90210") 
    }) 

    FormGroup 和 FormControl 都继承自同一个祖先AbstractControl。这意味检查 personInfo 的状态或值就像检查单个FormControl那么容易：

## FormBuilder
    FormBuilder是一个名副其实的表单构建助手。你应该还记得，表单是由FormControl和FormGroup构成的，而FormBuilder则可以帮助我们创建它们（你可以把它看作一个“工厂”对象）。
    通过在组件类上声明带参数的constructor，我们注入了一个FormBuilder。

    <form [formGroup]="myForm">

    当使用FormsModule时，NgForm会自动应用于<form>元素上。但其实有一个例外：NgForm不会应用到带formGroup属性的<form>节点上。
    需要记住以下两点。

    如果想隐式创建新的FormGroup和FormControl，使用：

    ngForm
    ngModel

    如果要绑定一个现有的FormGroup和FormControl，使用：

    formGroup
    formControl

添加验证
    let control = new FormControl('sku', Validators.required);
    constructor(fb: FormBuilder) { 
        this.myForm = fb.group({ 
            'sku': ['', Validators.required] 
        }); 
        this.sku = this.myForm.controls['sku']; 
    }

## EventEmitter
FormGroup和FormControl都带有EventEmitter（事件发射器），我们可以通过它来观察变化。
想监听控件的变化，我们要：
(1) 通过调用control.valueChanges访问到这个EventEmitter；
(2) 然后使用.subscribe方法添加一个监听器。

## Reactive Form
* Reactive Form创建方法
* 如何使用验证
* 自定义验证器

Reactive Form创建方法
    registerForm: FormGroup;
    constructor(private fb: FormBuilder) {}
    ngOnInit() {
        this.registerForm = this.fb.group({
            firstName: [''],
            lastName: [''],
        })
    }

    <form [formGroup]="registerForm" (ngSubmit)="handleSubmit(registerForm)">
        <label>FirstName:</label>
        <input formControlName="firstName">
        <label>LastName:</label>
        <input formControlName="lastName">
        <button type="submit">Submit</button>
    </form>
如何使用验证
    ngOnInit() {
        this.registerForm = this.fb.group({
            firstName: ['', Validators.required],
            lastName: ['', Validators.pattern('[A-Za-z0-9]*')],
        })
    }
如果一个控件需要多个验证器，可以把它们放在一个数组里面：
    lastName: ['', [Validators.pattern('[A-Za-z0-9]*'), Validators.required]]
如果你希望在输入时得到一些提示，可以这样写你的HTML：
    <form [formGroup]="registerForm" (ngSubmit)="handleSubmit(registerForm)">
        <label>FirstName:</label>
        <input formControlName="firstName">
        <p *ngIf="registerForm.controls.firstName.touched && registerForm.controls.firstName.invalid">
            This field is required!
        </p>
        <label>LastName:</label>
        <input formControlName="lastName">
        <p *ngIf="registerForm.controls.lastName.hasError('pattern')">
            Invalid input!
        </p>
        <button type="submit" [disabled]="!registerForm.valid">Submit</button>
    </form>
自定义验证器
    export function validateUrl(control: AbstractControl){
        if(control.value){
            if(!control.value.startsWith('www') || !control.value.includes('.io')){
            return {
                inValidUrl: true
            }
            }
        }
        return null;
    }
    ↑
    this.registerForm = this.fb.group({
        firstName: ['', Validators.required],
        lastName: ['', [Validators.pattern('[A-Za-z0-9]*'), Validators.required]],
        website: ['', validateUrl], // <---
    })
    ↑
    <label>Website:</label>
    <input formControlName="website">
        <p *ngIf="registerForm.controls.website.hasError('inValidUrl')">  //<---
        Url must starts with www and includes .io
    </p>
