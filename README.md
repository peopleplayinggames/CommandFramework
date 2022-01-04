I created a very lightweight annotation based command system that works similar to Bukkit's events system. It removes the necessity to add the command to your plugin.yml but will still allow you to set descriptions and usages through code.

This framework is now part of a bigger project called the MinezRC Framework. It is a framework that can be shaded into plugins to allow for all in one features from JSON messages to SignInput GUIs to packet receiving/sending. It can be found here: https://git.minezrc.net/minezrc-devs/framework

***Below edited by Wising***

Example Usage:

*This example uses Lombok, a free and extremely useful [maven](https://projectlombok.org/setup/maven) / [gradle](https://projectlombok.org/setup/gradle) tool for creating getters and setters*

Using this in your project:
```
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>

<dependency>
    <groupId>com.github.peopleplayinggames</groupId>
    <artifactId>CommandFramework</artifactId>
    <version>master-19b35f342d-1</version>
</dependency>
```

**Main plugin class**

```java
public class DemoPlugin extends JavaPlugin {

    @Getter private static DemoPlugin instance;
    @Getter private CommandFramework framework;

    public void onEnable() {
        instance = this;
        framework = new CommandFramework(this);
        
        // Initialize command class from below example
        new DemoCommands();
    }
}
```

**Command class**
```java
public class DemoCommands {

    public DemoCommands() {
        // This will register all the @Command methods in this class. It can be done with any object
        // Note: Commands do not need to be registered in plugin.yml
        DemoPlugin.getInstance().getFramework().registerCommands(this);
    }

    @Command(name = "test", aliases = { "testing" }, description = "This is a test command", usage = "This is how you use it")
    public void test(CommandArgs args) {
        args.getSender().sendMessage("This is a test command");
    }
}
```
This example will create a command called test and register it. It will set the description and usage of the command as well. Notice the aliases option. Aliases will be registered the same way as the regular command and are alternate commands that the method will be invoked with. Here is an example of a sub command:

```
@Command(name = "test.sub", aliases = { "test.subcommand"})
public void testSub(CommandArgs args) {
    args.getSender().sendMessage("This is a test subcommand");
}
```

This will create a sub command of test and will be executed when someone sends the command '/test sub' or '/test subcommand'. Descriptions and usages also work with these.

**Using completers**

Completers allow users to tab complete with your data for easy access and use of commands

```java
import com.minnymin.command.CommandArgs;
import com.minnymin.command.Completer;

import java.util.ArrayList;
import java.util.Arrays;

public class GameModeCommands {

    public GameModeCommands() {
        // This will register all the @Command methods in this class. It can be done with any object
        // Note: Commands do not need to be registered in plugin.yml
        DemoPlugin.getInstance().getFramework().registerCommands(this);
    }

    @Command(name = "gamemode", inGameOnly = true)
    public void gameMode(CommandArgs args) {
        // Implementation
    }

    // Completer
    @Completer(name = "gamemode")
    public List<String> gameModeCompleter(CommandArgs cmd) {
        if (cmd.getArgs().length < 2) {
            // If there is less than two arguments, return the first arguments completers
            return Arrays.asList("survival", "adventure", "spectator", "creative");
        }
        
        return new ArrayList<>();
    }
}
```

**Registering commands in a simpler way**

You can do this by creating a class in your plugins utilities like the following

```java
import com.minnymin.command.Command;
import com.minnymin.command.CommandArgs;
import org.bukkit.Bukkit;

public class CommandRegister {
    public CommandRegister() {
        // Adjust instance / class below for your plugin
        // Registers all commands in the class that extends CommandRegister
        DemoPlugin.getInstance().getFramework().registerCommands(this);
    }
}

// Usage:
public class DemoCommand extends CommandRegister {

    // No need for a constructor to register commands, already done.

    @Command(name = "test")
    public void test(CommandArgs cmd) {
        Bukkit.broadcast("pog");
    }
}
```

If you need any help with this framework just join the PPG discord and open a ticket, I (Wising) am glad to help after using this framework for over a year now
[Discord](https://ppgmedia.uk/discord)

If you wish to edit this and help out with updating it feel free I'd really appreciate it.

Full credit and thanks to the original authors (source [Repository](https://github.com/mcardy/CommandFramework)) & [NulledCodeDev](https://github.com/NulledCodeDev) who this more documented version is forked from