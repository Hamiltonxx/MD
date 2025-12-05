# Install
```
export HTTP_PROXY=127.0.0.1:58591
mkdir ninjafood
cd ninjafood
npx create-next-app@latest .
npm run dev
```
globals.css
```
@tailwind base;
@tailwind components;
@tailwind utilities;
```
page.tsx
```
export default function Home() {
  return (
    <h1 className="text-3xl font-bold underline">
      Hello world!
    </h1>
  )
}
```

# Core Concepts
## Utility-First Fundamentals
```
export default function Home() {
  return (
    <div className="py-8 px-8 max-w-sm mx-auto bg-white rounded-xl shadow-lg space-y-2 sm:py-4 sm:flex sm:items-center sm:space-y-0 sm:space-x-6">
      <img className="block mx-auto h-24 rounded-full sm:mx-0 sm:shrink-0" src="https://picsum.photos/300.webp" alt="Woman's Face" />
      <div className="text-center space-y-2 sm:text-left">
        <div className="space-y-0.5">
          <p className="text-lg text-black font-semibold">
            Erin Lindford
          </p>
          <p className="text-slate-500 font-medium">
            Product Engineer
          </p>
        </div>
        <button className="px-4 py-1 text-sm text-purple-600 font-semibold rounded-full border border-purple-200 hover:text-white hover:bg-purple-600 hover:border-transparent focus:outline-none focus:ring-2 focus:ring-purple-600 focus:ring-offset-2">Message</button>
      </div>
    </div>
  )
}
```
## Hover, Focus, and Other States
```
return (
    <a href="#" className="group block max-w-xs mx-auto rounded-lg p-6 bg-white ring-1 ring-slate-900/5 shadow-log space-y-3 hover:bg-sky-500 hover:ring-sky-500">
      <div className="flex items-center space-x-3">
        <FolderOpenIcon className="h-6 w-6 stroke-sky-500 group-hover:stroke-white" />
        <h3 className="text-slate-900 group-hover:text-white text-sm font-semibold">New Project</h3>
      </div>
      <p className="text-slate-500 group-hover:text-white text-sm">Create a new project from a variety of starting templates.</p>
    </a> 
 )
```
## Responsive Design
```
return (
	<img className="w-32 md:w-64 lg:w-auto" src="https://picsum.photos/768.webp" />
)
```
There are five breakpoints by default: sm(640), md(768), lg(1024), xl(1280), 2xl(1536)  
```
return (
    <div className="max-w-md max-auto bg-white rounded-xl shadow-md overflow-hidden md:max-w-2xl">
      <div className="md:flex">
        <div class="md:shrink-0">
          <img className="h-48 w-full object-cover md:h-full md:w-48" src="https://picsum.photos/768.webp" alt="Modern building architecture" />
        </div>
        <div className="p-8">
          <div className="uppercase tracking-wide text-sm text-indigo-500 font-semibold">Company retreats</div>
          <a href="#" className="block mt-1 text-lg leading-tight font-medium text-black hover:underline">Incredible accommodation for your team</a>
          <p className="mt-2 text-slate-500">Looking to take your team away on a retreat to enjoy awesome food and take in some sunshine? We have a list of places to do just that. You never wake a man that sleeping.</p>
        </div>
      </div>
    </div>
  )
```
Don't use `sm:` to target mobile devices.  
Use unprefixed utilities to target mobile, and override them at larger breakpoints.  
```
<div class="text-center sm:text-left"></div>
``` 
## Dark Mode
layout.tsx
```
dark:bg-black
```
## Reusing Styles
```
function Notification({ imageUrl, imageAlt, title, message }) {
	return (
		<div className="p-6 max-w-sm mx-auto bg-white rounded-xl shadow-md flex items-center space-x-4">
			<div className="shrink-0">
				<img className="h-12 w-12" src={imageUrl.src} alt={imageAlt}>
			</div>
			<div>
				<div className="text-xl font-medium text-black">{title}</div>
				<p className="text-slate-500">{message}</p>
			</div>
		</div>
	)
}
```
## Adding Custom Styles
```
<ul role="list">
	{#each items as item}
		<li class="lg:[&:nth-child(3)]:hover:underline">{item}</li>
	{/each}
</ul>
```
## Functions & Directives
Use the '@layer' directive to tell Tailwind which "bucket" a set of custom styles belong to. 'base','components','utilities'.  
Use the '@apply' to inline any existing utility classes into your own custom CSS.  
Use the '@config' directive to specify which config file Tailwind should use when compiling that CSS file

# Customization
# Base Styles

# Layout
## Aspect Ratio
aspect-auto, aspect-square, aspect-video  
## Container
container: width:100%; sm: max-width:640px; md: max-width:768px; lg: max-width:1024px; xl: max-width:1280px; 2xl: max-width:1536px;  
Tailwind's container does not center itself automatically and does not have any built-in horizontal padding.  
```
<div class="container mx-auto px-4">
</div>
<div class="md:container md:mx-auto">
</div>
```
## Columns
Utilities for controlling the number of columns within an element.  
```
<div class="columns-2 md:columns-3">
</div>
```
## Break After
Utilities for controlling how a column or page should break after an element.
## Box Sizing
Use the 'box-border' utility to set an element's 'box-sizing' to 'border-box', telling the browser to include element's borders and padding when you give it a height or width.
## Display
inline-block: display inline but have width,height,padding,margin like block.
## Overflow
'overflow-visible': showing content that overflows  
'overflow-hidden': clip any content within an element that overflows the bounds of that element  
'overflow-auto': add scrollbars to an element in the event that its content overflows the bounds of that element
'overflow-x-auto'/'overflow-y-auto': allow horizontal/vertical scrolling if needed  
'overflow-scroll': always shows the scrollbars  
'overflow-x-scroll'/'overflow-y-scroll':
## Position
static, fixed, absolute, relative, sticky  
1. static(default): top,right,bottom,left have no effect.  
2. relative: relative to its normal position in the document. use top,right,bottom,left to move it from its original position.  
3. absolute: relative to its nearest positioned ancestor. The element is removed from the normal document flow.  
4. fixed: relative to the browser window. The element is removed from the normal document flow.  
5. sticky: behaves like 'relative' until the element reaches a specified position, then fixed.
## Top/Right/Bottom/Left
```html
<!-- Pin to top left corner -->
<div class="relative h-32 w-32">
	<div class="absolute left-0 top-0 h-16 w-16 ...">01</div>
</div>
<!-- Span top edge -->
<div class="relative h-32 w-32">
	<div class="absolute inset-x-0 top-0 h-16 ...">02</div>
</div>
<!-- Pin to top right corner -->
<div class="relative h-32 w-32">
	<div class="absolute top-0 right-0 h-16 w-16 ...">03</div>
</div>
<!-- Span left edge -->
<div class="relative h-32 w-32">
	<div class="absolute inset-y-0 left-0 w-16">04</div>
</div>
<!-- Fill entire parent -->
<div class="relative h-32 w-32">
	<div class="absolute inset-0">05</div>
</div>
<!-- Span right edge -->
<div class="relative h-32 w-32">
	<div class="absolute inset-y-0 right-0 w-16">06</div>
</div>
<!-- Pin to bottom left corner -->
<div class="relative h-32 w-32">
	<div class="absolute bottom-0 left-0 h-16 w-16">07</div>
</div>
<!-- Span bottom edge -->
<div class="relative h-32 w-32">
	<div class="absolute inset-x-0 bottom-0 h-16">08</div>
</div>
<!-- Pin to bottom right corner -->
<div class="relative h-32 w-32">
	<div class="absolute bottom-0 right-0 h-16 w-16">09</div>
</div>

<!-- negative values -->
<div class="relative h-32 w-32">
	<div class="absolute h-14 w-14 -left-4 -top-4"></div>
</div>
```
## Visibility
visible, invisible, collapse
## Z-index
```html
<div class="z-40">05</div>
<div class="z-30">04</div>
<div class="z-20">03</div>
<div class="z-10">02</div>
<div class="z-0">01</div>
```

# Flexbox & Grid
## Flex Basis
controlling the initial size of flex items.  
basis-1,2,3,4,5,6,8,10,12,14,16,20,24,28,32,96,auto,1/2,1/3,1/4,3/4,3/5,1/12,full  
```html
<div class="flex">
	<div class="basis-1/4 md:basis-1/3">01</div>
	<div class="basis-1/4 md:basis-1/3">02</div>
	<div class="basis-1/2 md:basis-1/3">03</div>
</div>
```
## Flex Direction
flex-row flex-row-reverse flex-col flex-col-reverse 
## Flex Wrap
flex-wrap flex-nowrap
## Flex
flex: [flex-grow] [flex-shrink] [flex-basis]  
flex-1: flex: 1 1 0; grow, shrink, initial size is set to 0  
flex-none: flex: 0, 0, auto; not grow, not shrink, the initial size is based on its content  
flex-auto: flex: 1 1 auto; grow, shrink, the initial size is based on its content  
flex-initial: 0 1 auto; not grow, shrink, the initial size is based on its content
## Flex Grow
```html
<div class="flex">
	<div class="flex-none w-14 h-14">01</div>
	<div class="grow h-14">02</div>
	<div class="flex-none w-14 h-14">03</div>
</div>
```
## Flex Shrink
## Order
## Grid Template Columns
Utilities for specifying the columns in a grid layout  
'grid-cols-n' to create grids with n equally sized columns.
```html
<div class="grid grid-cols-4 gap-4">
	
</div>
```
'col-span-n' to make an element span n columns.  
```html
<div class="grid grid-cols-3 gap-4">
	<div>01</div>
	<div class="col-span-2">02</div>
	<div class="col-span-2">03</div>
	<div>04</div>
</div>
```
'col-start-s','col-end-e' to make an element start or end at the nth grid line.
```html
<div class="grid grid-cols-6 gap-4">
	<div class="col-start-2 col-span-4">01</div>
	<div class="col-start-1 col-end-3">02</div>
	<div class="col-end-7 col-span-2">03</div>
	<div class="col-start-1 col-end-7">04</div>
</div>
```
## Grid Template Rows
'grid-rows-n' to create grids with n equally sized rows.

