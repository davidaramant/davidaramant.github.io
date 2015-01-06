---
layout: post
title:  "Snippet Sandbox"
---
Oftentimes when coding you come into a situation where you're not 100% sure what some code is going to do - maybe you're using an unfamiliar framework API, or the LINQ query is totally insane, or the code is just too hard to follow.  So, what do you do?  If you're like I used to be, you spend a lot of time reasoning about what the code should do and, well, end up guessing.

Now, if you're working in greenfield code or you have the priviledge of being in a well-maintained codebase you can probably write a unit test to quickly cover the code in question (and only that code).  Unfortunately, a lot of time we are stuck with less than optimal situations where the guess cycle (write some code, see what it does) just takes way too long - maybe the tests are ponderously slow, maybe you're messing with a class that is only indirectly tested by something "far away" code-wise that would make it extremely awkward to see the output or to pass in interesting input.

#Snippet Compiler
A long time ago I found a program called [Snippet Compiler](http://www.sliver.com/dotnet/snippetcompiler/) that seemed perfect for situations like this.  You could easily fire it up, type a few lines of code, and instantly see the results.

Unfortunately Snippet Compiler has two significant flaws:

* It's not Visual Studio, so not all my ingrained hotkeys were supported, there was no ReSharper, etc.
* It's hopelessly old (.NET 3.5)

Now, why does something like Snippet Compiler exist in the first place?

* Starting Visual Studio is slow
* Creating a new C# program is annoyingly heavyweight just to test a few lines of code

Well, time has fixed (improved, at least) the first problem - starting a modern VS instance on a SSD is lightyears better from starting VS2008 back in the day on a spinning-rust drive with some archaic CPU from the stoneage and microscopic amounts of RAM.

The second problem is still there (although it will be mitigated somewhat in the future with things like REPLs for C# becoming available).

However, why do we need to create a new sandbox everytime?  Why not just make it once and keep it around?  The rise of personal source control systems like git solves this second annoyance - we can easily create our sandbox once, save off our code doodles if we want to, and most importantly, it's trivial to wipe everything clean.

# The Sandbox

Here is my current bare-bones snippet program:

{% highlight c# %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.IO;
using System.Text;

namespace ConsoleRunner
{
    sealed class Program
    {
        public static void RunSnippet( string[] args )
        {

        }

        #region Helper methods

        private static string Desktop
        {
            get 
            { 
                return Environment.GetFolderPath(
                    Environment.SpecialFolder.DesktopDirectory ); 
            }
        }

        public static void Main( string[] args )
        {
            try
            {
                RunSnippet( args );
            }
            catch( Exception e )
            {
                Console.WriteLine( "---\nEXCEPTION:\n" + e + "\n" );
            }
            finally
            {
                Console.Write( "Press any key to continue..." );
                Console.ReadKey();
            }
        }

        private static void WL()
        {
            Console.WriteLine();
        }

        private static void WL( object text, params object[] args )
        {
            if( text == null )
            {
                Console.WriteLine( "null" );
            }
            else
            {
                Console.WriteLine( text.ToString(), args );
            }
        }

        private static string RL()
        {
            return Console.ReadLine();
        }

        private static void Break()
        {
            System.Diagnostics.Debugger.Break();
        }
        
        #endregion
    }
}
{% endhighlight %}  

This was shamelessly lifted from Snippet Compiler with a couple of minor tweaks.  The highlights of the program are:

* The RunSnippet method is not the Main method.  We want a 'clean' place to write code without having to worry about catching exceptions or keeping the output window open.  Write code, mash F5, see the results - that's the goal.
* There are shortcuts for input/output.  Writing Console.WriteLine all the time gets real old, real fast.
* There is a shortcut for the Desktop location.  I use this as a basis for utility program all the time and it's nice to just slap a file on the desktop and easily be able to get it's filepath without writing that annoying code.

I actually have a couple more utility methods in this class, but I'll cover those in future posts.

