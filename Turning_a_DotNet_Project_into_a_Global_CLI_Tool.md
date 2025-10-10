# 🧰 Turning a .NET Project into a Global CLI Tool

## Using *Reflo* as a Practical Example

Building a CLI tool in .NET is one of the most empowering things you can do as a developer.  
You can transform a normal console project into a **globally accessible command**—just like `dotnet`, `git`, or `ef`.

This guide walks you through the **entire process** of turning a .NET console app into a global CLI tool, using **Reflo** (a refactoring automation tool) as a real-world example.

---

## 1. Prepare Your Project

You can start with any console project.

**Example structure:**

```
RosylnHello/
├── Reflo/
│   ├── Reflo.csproj
│   ├── Program.cs
│   └── ... (other files)
└── TestLibrary/
    └── Sample .cs files
```

**Your `Program.cs` entry point:**

```csharp
namespace Reflo
{
    internal class Program
    {
        static void Main(string[] args)
        {
            if (args.Length == 0)
            {
                Console.WriteLine("Usage:");
                Console.WriteLine("  reflo apply <RuleName> <Path>");
                Console.WriteLine("  reflo revert <Path>");
                Console.WriteLine("  reflo list-rules");
                return;
            }

            string command = args[0].ToLower();

            switch (command)
            {
                case "apply":
                    RefactorEngine.ApplyRuleByName(args[1], args[2]);
                    break;

                case "revert":
                    RefactorEngine.RevertAll(args.Length > 1 ? args[1] : Directory.GetCurrentDirectory());
                    break;

                case "list-rules":
                    RefactorEngine.ListRules();
                    break;

                default:
                    Console.WriteLine($"Unknown command '{command}'");
                    break;
            }
        }
    }
}
```