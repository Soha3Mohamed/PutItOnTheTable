# 🧰 Turning a .NET Project into a Global CLI Tool  
### Using *Reflo* as a Practical Example

Building a CLI tool in .NET is one of the most empowering things you can do as a developer.  
You can transform a normal console project into a **globally accessible command** — just like `dotnet`, `git`, or `ef`.

This guide walks you through the **entire process** of turning a .NET console app into a global CLI tool, using **Reflo** (a refactoring automation tool) as a real example.

---

## 🪜 1. Prepare Your Project

You can start with any console project.

**Example structure:**
```
RosylnHello/
├── Reflo/
│    ├── Reflo.csproj
│    ├── Program.cs
│    └── ... (other files)
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

---

## ⚙️ 2. Configure the `.csproj` File

Edit `Reflo.csproj` to make it **packable as a .NET tool**:

```xml
<PropertyGroup>
  <OutputType>Exe</OutputType>
  <TargetFramework>net8.0</TargetFramework>

  <!-- These properties make your app a CLI tool -->
  <PackAsTool>true</PackAsTool>
  <ToolCommandName>reflo</ToolCommandName>
  <PackageId>Reflo</PackageId>
  <PackageOutputPath>./nupkg</PackageOutputPath>
</PropertyGroup>
```

**Explanation:**

- `PackAsTool` → tells .NET this project is a tool.
- `ToolCommandName` → defines the actual command (e.g. `reflo`).
- `PackageOutputPath` → where to output the NuGet package.

---

## 📦 3. Pack the Tool

From inside the `Reflo` directory:

```bash
dotnet pack -c Release
```

This creates a `.nupkg` file under:

```
Reflo\nupkg\Reflo.1.0.0.nupkg
```

This file contains your compiled app and metadata — it’s your installable CLI package.

---

## 🧩 4. Install the Tool Globally

Install it from your local folder:

```bash
dotnet tool install --global --add-source ./nupkg Reflo
```

If that fails, use the full path:

```bash
dotnet tool install --global --add-source "C:\Users\<you>\source\repos\RosylnHello\Reflo\nupkg" Reflo
```

✅ Once installed, `reflo` becomes a recognized command system-wide.

---

## 🔍 5. Verify Installation

Check installed tools:

```bash
dotnet tool list -g
```

You should see:

```
Package Id  Version  Commands
---------------------------------
reflo       1.0.0    reflo
```

---

## 🚀 6. Run Your CLI Tool

Now you can use `reflo` from **any directory**:

```bash
reflo apply InternalToPublic "C:\Users\source\repos\TestLibrary"
```

or

```bash
reflo list-rules
```

---

## 🔄 7. Updating the Tool During Development

When you make code changes:

```bash
dotnet pack -c Release
dotnet tool uninstall -g Reflo
dotnet tool install --global --add-source ./nupkg Reflo
```

This ensures you’re using the latest version.

---

## 🧠 8. What’s Happening Behind the Scenes

| Step                  | Concept      | Description                                                                 |
| --------------------- | ------------ | --------------------------------------------------------------------------- |
| `dotnet pack`         | Packaging    | Creates a `.nupkg` file containing your compiled app and metadata.          |
| `PackAsTool`          | Tool flag    | Tells .NET this project should behave as a CLI tool.                        |
| `ToolCommandName`     | Command name | Defines the word you’ll type in the terminal (`reflo`).                     |
| `dotnet tool install` | Registration | Registers the tool globally so your system recognizes the command.          |
| `reflo <command>`     | Execution    | .NET automatically runs your packaged executable when you type the command. |

---

## 🧭 9. Notes and Tips

- You can publish your `.nupkg` to **NuGet.org** to make it publicly installable.
- Each subcommand (like `apply`, `revert`, `list-rules`) can grow into a modular CLI system.
- Use libraries like:
  - [`System.CommandLine`](https://learn.microsoft.com/en-us/dotnet/standard/commandline/)
  - [`Spectre.Console.Cli`](https://spectreconsole.net/cli/)
  to handle complex command structures and argument parsing elegantly.
- You can colorize output, add emojis, and log results for better UX.

---

## ✅ Example Workflow Recap

```bash
cd Reflo
dotnet pack -c Release
dotnet tool install --global --add-source ./nupkg Reflo

# Run the tool
reflo list-rules
reflo apply InternalToPublic "C:\MyProject"
```

**Output Example:**

```
✔ Updated: Employee.cs (backup saved)
♻ Reverted: Employee.cs
✅ Revert complete!
```

---

## 🎯 Final Thoughts

Turning `Reflo` into a CLI wasn’t just about convenience —
it was about thinking like a **tool builder**.

Now you can:

- Apply refactor rules to entire projects right from the terminal.
- Distribute your logic as a reusable global command.
- Treat your creation like a true developer utility.

Once you grasp this pattern, you can make **any .NET project** behave like a real CLI — portable, scriptable, and ready to share.

---

**Author:** Soha Mohamed   
**Project:** Reflo — Automated C# Refactoring Tool  
**License:** MIT

---
