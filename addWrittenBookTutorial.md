# Writing a book trough programming (Fabric Minecraft) #

##### Introduction #####

Sometimes, you may want to simply create a vanilla item but with your 
own data in it.
Sure, you could make your own item that extends the WrittenBook class, but sometimes
you just don't need a entirely new item. Let's take a book for example.

OBSERVE: This tutorial won't teach you JSON that minecraft accepts, but a example of my own JSON for 1 page is included.
##### Creating the class #####

We will make a utility class of our own so we can create the book anywhere by simply doing
BookCreator bookcreator = new BookCreator(Item);
```Java
public class BookCreator {
 	
	public ItemEntity BookCreator(Item unwrittenbook) {
		//Our code to do the MAGIC
		return ItemEntity;
	}
    
}
```

Now you have a class with a method in it
that will return a ItemEntity.
In order to spawn a item, it must be a ItemEntity.

Now. You cannot just up and do WrittenBook myBook = new WrittenBook(); 
sicne the item itself is already registered as vanilla. If you wanted to add or change the
actual item itself, you are going to need mixins since it's vanilla or make a entirely new item that extends it
and register that.

No, we need to grab the book item from the registery itself.
This is how you grab from the registry.
```Java
BookCreator bookcreator = new BookCreator((Item) Registry.ITEM.get(new Identifier("written_book")));
```
This grabs the written_book from the registry, and casts it to a Item, which is what we need in order to start
manipulating our new "copy".

Now look at this code.
```Java
public class BookCreator {
 	
	public ItemEntity createBook(Item unwrittenbook) {
        ItemStack letterStack = new ItemStack(unwrittenbook);
        NbtList pages = new NbtList();
        //More code to come.
		return ItemEntity;
	}
    
}
```
First, we insert the item unwrittenbook (our parameter) into a ItemStack so we can start creating tags.
There's 3 obligatory tags we need to set on a written_book:

title
author
pages

I have not tested if pages can be empty like so: {}
But I digress. Minecraft won't let you create a book with 1 page that has no characters. 
 
Let's finish up the parameters we need for our method.
```Java
public class BookCreator {
	
	public ItemEntity createBook(Item unwrittenbook,String title, String author, List<NbtString> _pages) {
		ItemStack letterStack = new ItemStack(unwrittenbook);
        
        NbtList filledPages = addPages(_pages);
        //More code to come.
        return ItemEntity;
	}

}
```
We have created the parameters we need. title can be 15 letters long and must be minimum 1 character (validating this is your exercise).
author can be between 3 to 16 characters long. Again validating these things is something I leave to you as a exercise.
As you can see, we have "pages = addPages(_pages);"
This is a method we are calling in the BookCreator class. So let's create it.
```Java
	public NbtList addPages(List<NbtString> _pages) {
		NbtList filledpages = new NbtList();
		for(NbtString page : _pages)
{  
	filledpages.add(page);
}
return filledpages;
	}
```
We grab the list of NbtStrings and add them to our NbtList and then return the NbtList to filledPages
in the createBook method.

Let's add our 2 string parameters and the pages.
```Java
public class BookCreator {
	
	public ItemEntity createBook(Item unwrittenbook,String title, String author, List<NbtString> _pages) {
		ItemStack myBookStack = new ItemStack(unwrittenbook);
        
        NbtList filledPages = addPages(_pages);
        myBookStack.putSubTag("title", NbtString.of(title));
        myBookStack.putSubTag("author", NbtString.of(author));
        myBookStack.putSubTag("pages", filledPages);
        //More code to come.
        return ItemEntity;
	}


	public NbtList addPages(List<NbtString> _pages) {
		//Code
return filledpages;
	}
}
```

We have now added the SubTags to our written_book.
We have everything neccesary to spawn the book except one thing. We must now insert this
ItemStack into an ItemEntity so we can spawn it. 
We also need at minimum to give it the world. So we need one last parameter on
createBook too.

Thus the full and finished BookCreator class is:
```Java
public class BookCreator {
	
	public boolean createBook(Item unwrittenbook,String title, String author, List<NbtString> _pages, World world) {
		ItemStack myBookStack = new ItemStack(unwrittenbook);
        
        NbtList filledPages = addPages(_pages);
        myBookStack.putSubTag("title", NbtString.of(title));
        myBookStack.putSubTag("author", NbtString.of(author));
        myBookStack.putSubTag("pages", filledPages);
        ItemEntity myBookEntity = new ItemEntity(world, 500, 120, 500, myBookStack);
        return world.spawnEntity(myBookEntity);
	}


	public NbtList addPages(List<NbtString> _pages) {
		NbtList filledpages = new NbtList();
		for(NbtString page : _pages)
{  
	filledpages.add(page);
}
return filledpages;
	}
}
```
This would spawn the book at X 500, Y 120, Z 500 in the world you passed to the World world parameter in createBook.
##### How to use the class #####

```Java
//Some code to get a world.
List<NbtString> pages = new ArrayList<>();
//This is the free example JSON I was talking about.
pages.add(NbtString.of("{"extra":[{"bold":true,"color":"dark_green","text":"Greetings! \n\nWe have observed your village. But we feel like there's to many living there already.\n\nWe wish you all the best with your\nvillage."},{"text":"\n\n"},{"italic":true,"text":"Stranger"}],"text":""}");
//You can keep adding pages. Up to 100 of them (as of the time of writing 2021-06-30) in Java Edition.
BookCreator bookcreator = new BookCreator();
boolean bookCreatorSuccess = bookcreator.createBook((Item) Registry.ITEM.get(new Identifier("written_book")),"Book of wonder","Yortni",pages,world);;
```
And then do whatever you want with the boolean to verify, now fuck off I am drinking beer. This is it. 

A last hint: Utility classes are better off being static and the registered items are static.
But you should atleast try some basic java programming before, so you should know how to make a static class etc.
I was drunk when I made this tutorial and that's why the utility class is created like a object...
