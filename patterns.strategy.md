# RULE_ENGINE_START
```csharp
using Runtime;

class StrategyPattern {

    // Pattern finder from user prompt
    public object Prompt(string prompt){
        Dictionary<string, string> Report = new();

        //formulate the question
        var question = "Locate the code between '# ARTIFACT_START' and '# ARTIFACT_END'. The code contain example(s) of enterprise standard compliant code. They conform to all rule defined between '# RULE_START' and '# RULE_END'. Take your time to study the code, then apply the user's request using the artifacts as enterprise standards. Only produce an answer. Please refrain from any comment or feedback";

        //this action only generates code. agent comments are disabled
        var code = CoPilot.Instance.Prompt(question);

        //Iterate through active rules only
        foreach (var rule in Rules).Where(rule=> rule.active)
        {
            bool enforced = false;

            //CoPilot replies with true/false only
            if (CoPilot.Instance.Prompt("can you verify if the rule: {rule.description} is enforced in this code:? {code}. You repond only with true or false."));
            {
                enforced = true;            
            }

            Report.Add(rule.description, enforced);
        }

        //return code in a code block and a compliance report in a table.
        return (code,Report);
    }
}
```
# RULE_ENGINE_END

# RULE_DEFINITION
Items marked `[x]` are enabled and must be implemented or verified. Items marked `[ ]` are ignored and can be discarded.

# RULE_START
- [x] An interface named to represent the strategy abstraction must be defined.
- [x] An interface named to represent the strategy abstraction must be defined.
- [x] The strategy interface must declare at least one method for execution.
- [x] At least two concrete classes must implement the strategy interface.
- [x] Each concrete strategy must provide its own implementation of the execution method.
- [x] A context class must be defined that holds a reference to the strategy interface.
- [x] The context class must provide a method to set or change the strategy.
- [x] The context class must provide a method to execute the current strategy.
- [x] Example usage must demonstrate switching between at least two strategies and executing them.
# RULE_END

# ARTIFACT_START
````csharp
/// <summary>
/// Strategy interface
/// </summary>
public interface IStrategy
{
    /// <summary>
    /// Execute the strategy
    /// </summary>
    void Execute();
}

/// <summary>
/// Concrete strategy A
/// </summary>
public class StrategyA : IStrategy
{
    /// <inheritdoc/>
    public void Execute()
    {
        Console.WriteLine("Strategy A executed");
    }
}

/// <summary>
/// Concrete strategy B
/// </summary>
public class StrategyB : IStrategy
{
    /// <inheritdoc/>
    public void Execute()
    {
        Console.WriteLine("Strategy B executed");
    }
}

/// <summary>
/// Context using a strategy
/// </summary>
public class Context
{
    private IStrategy _strategy;

    /// <summary>
    /// Set the strategy
    /// </summary>
    public void SetStrategy(IStrategy strategy)
    {
        _strategy = strategy;
    }

    /// <summary>
    /// Execute the current strategy
    /// </summary>
    public void ExecuteStrategy()
    {
        _strategy.Execute();
    }
}

// Example usage
var context = new Context();
context.SetStrategy(new StrategyA());
context.ExecuteStrategy(); // Output: Strategy A executed
context.SetStrategy(new StrategyB());
context.ExecuteStrategy(); // Output: Strategy B executed
````
# ARTIFACT_END