

> [!NOTE]
> This repository hosts the version information for any vandal development plugins. It provides a simple way to check for updates by fetching the version number from GitHub Pages.

> [!IMPORTANT]
> ## How It Works
> 
> 1. The latest version of the plugin is stored in a plain text file called `version-<plugin>.txt`.
> 2. The version file is hosted using **GitHub Pages**.
> 3. The plugin fetches this file to compare the current version with the latest available version.

## Steps to Fetch the Latest Version in Your Plugin

### 1. Set Up new Product

- Add a `version-<plugin>.txt` file to the repository with your plugin's latest version number.
- Once enabled, your version file will be hosted at: `https://snowypy.github.io/vandal-version/version-<plugin>.txt`.

### 2. Fetch Version in the Plugin

In any Vandal Development plugin (written in Kotlin or Java), you can use an HTTP request to fetch the version file from GitHub Pages and compare it with your current version.

Here’s an example code in **Kotlin** to check for updates:

```kotlin
import org.bukkit.Bukkit
import org.bukkit.plugin.java.JavaPlugin
import java.net.HttpURLConnection
import java.net.URL
import java.util.logging.Level

class YourPlugin : JavaPlugin() {
    private val updateUrl = "https://snowypy.github.io/vandal-version/version-<plugin>.txt"

    override fun onEnable() {
        checkForUpdates()
    }

    private fun checkForUpdates() {
        val url = URL(updateUrl)

        Bukkit.getScheduler().runTaskAsynchronously(this) {
            try {
                val connection = url.openConnection() as HttpURLConnection
                connection.connectTimeout = 5000
                connection.readTimeout = 5000

                val latestVersion = connection.inputStream.bufferedReader().readLine()

                if (latestVersion != this.description.version) {
                    logger.log(Level.WARNING, "A new version ($latestVersion) is available! You are running ${description.version}.")
                } else {
                    logger.log(Level.INFO, "You are using the latest version!")
                }

                connection.disconnect()
            } catch (e: Exception) {
                logger.log(Level.SEVERE, "Error while checking for updates: ${e.message}")
            }
        }
    }
}
```

# Notify Users (optional)

```kotlin
private fun notifyUpdateAvailable(latestVersion: String) {
    for (player in server.onlinePlayers) {
        if (player.hasPermission("<plugin>.updatechecker")) {
            player.sendMessage("&eA new version of the plugin ($latestVersion) is available!")
        }
    }
}
```
