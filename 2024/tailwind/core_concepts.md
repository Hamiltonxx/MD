# Utility-First Fundamentals
```html
<div class="p-6 max-w-sm mx-auto bg-white rounded-xl shadow-lg flex items-center space-x-4">
    <div class="shrink-0">
        <img src="/img/chat-money.svg" alt="ChitChat Logo" class="size-12">
    </div>
    <div>
        <div class="text-xl font-medium text-black">ChitChat</div>
        <p class="text-slate-500">You have a new message!</p>
    </div>
</div>
```

```html
<div class="p-8 max-w-sm mx-auto bg-white rounded-xl shadow-lg space-y-2 sm:py-4 sm:flex sm:items-center sm:space-y-0 sm:space-x-6">
    <img src="https://picsum.photos/128.webp" alt="Woman's Face" class="block mx-auto h-24 rounded-full sm:mx-0 sm:shrink-0">
    <div class="text-center space-y-2 sm:text-left">
        <div class="space-y-0.5">
            <p class="text-lg text-black font-semibold">Erin Lindford</p>
            <p class="text-slate-500 font-medium">Product Engineer</p>
        </div>
        <button class="px-4 py-1 text-sm text-purple-600 font-semibold rounded-full border border-purple-200 hover:text-white hover:bg-purple-600 hover:border-transparent focus:outline-none focus:ring-2 focus:ring-purple-600 focus:ring-offset-2">Message</button>
    </div>
</div>
```

# Handling Hover,Focus,and Other States
```html
<button class="bg-sky-500 hover:bg-sky-700 px-4 py-2 rounded-3xl text-white">Save changes</button>
<button class="bg-violet-500 hover:bg-violet-600 active:bg-violet-700 focus:outline-none focus:ring focus:ring-violet-300 px-4 py-2 rounded-3xl text-white">Save changes</button>
```

```html
<ul role="list" class="p-6 divide-y divide-slate-200">
    {#each people as person}
    <!-- Remove top/bottom padding when first/last child -->
    <li class="flex py-4 first:pt-0 last:pb-0 odd:bg-white even:bg-slate-50">
        <img class="h-10 w-10 rounded-full" src="{person.imageUrl}" alt="" />
        <div class="ml-3 overflow-hidden">
            <p class="text-sm font-medium text-slate-900">{person.name}</p>
            <p class="text-sm text-slate-500 truncate">{person.email}</p>
        </div>
    </li>
    {/each}
</ul>
```

## Styling based on parent state
```html
<a href="" class="group block max-w-xs mx-auto rounded-lg p-6 bg-white ring-1 ring-slate-900/5 shadow-lg space-y-3 hover:bg-sky-500 hover:ring-sky-500">
    <div class="flex items-center space-x-3">
        <svg class="h-6 w-6 stroke-sky-500 group-hover:stroke-white" fill="none" viewBox="0 0 24 24"></svg>
        <h3 class="text-slate-900 group-hover:text-white text-sm font-semibold">New project</h3>
    </div>
    <p class="text-slate-500 group-hover:text-white text-sm">Create a new project from a variety of starting templates.</p>
</a>
```

```html
<ul role="list">
    {#each people as person}
    <li class="group/item hover:bg-slate-100">
        <img src="{person.imageUrl}" alt="" />
        <div>
            <a href="{person.url}">{person.name}</a>
            <p>{person.title}</p>
        </div>
        <a class="group/edit invisible hover:bg-slate-200 group-hover/item:visible" href="{person.phone}">
            <span class="group-hover/edit:text-gray-700">Call</span>
            <svg class="group-hover/edit:translate-x-0.5 group-hover/edit:text-slate-500"></svg>
        </a>
    </li>
    {/each}
</ul>
```
## Styling based on sibling state
```html
<form>
    <label class="block">
        <span class="block text-sm font-medium text-slate-700">Email</span>
        <input type="email" class="peer" />
        <p class="mt-2 invisible peer-invalid:visible text-pink-600 text-sm">Please provide a valid email address.</p>
    </label>
</form>
```
```html
<fieldset>
    <legend>Published status</legend>
    <input id="draft" class="peer/draft" type="radio" name="status">    
</fieldset>
```