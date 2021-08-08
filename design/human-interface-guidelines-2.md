# Human Interface Guidelines -App Architecture-

I'm fully convinced that many iOS developers didn't read this guideline. Otherwise, as it is mentioned, they would not ask people to rate their app as soon as we turn on their app. As I said before, this guideline is useful for developers. Check out these useful tips below.

## 1. Launching

The launch experience has a significant impact on the way people feel about your app. Regardless of the device people are using or how long it's been since they last opened your app, the launch experience should be fast and seamless.

***Provide a launch screen.*** The system displays your launch screen the moment your app starts and quickly replaces it with your app's first screen.

**Launch in the appropriate orientation.** If your app supports both portrait and landscape modes, it should launch using the device’s current orientation.

***Avoid asking for setup information up front.*** People expect apps to just work. Design your app for the majority of users and let the few that want a different configuration adjust settings to meet their needs.

**Avoid showing in-app licensing agreements and disclaimers.** Let the App Store display agreements and disclaimers so people can read them before downloading your app. 

**Restore the previous state when your app restarts.** Don't make people retrace steps to reach their previous location in your app. 

**Don’t encourage rebooting.** Restarting takes time and makes your app seem unreliable and hard to use.

***Avoid asking people to rate your app too quickly or too often.*** Asking for a rating soon after first launch — or too frequently while people are using your app — is annoying and likely to decrease the amount of useful feedback you receive.

## 2. Onboarding

Onboarding lets you welcome new users and reconnect with returning ones. An optional onboarding experience that’s fast, fun, and educational can help people get the most from your app without getting in their way.

**Provide onboarding that helps people enjoy your app, not just set it up.** People can appreciate the opportunity to learn more about your app, but they also expect it to just work.

**Get to the action quickly.** After the system replaces your launch screen with your initial app screen, let people dive right in and start enjoying your app. 

**Anticipate the need for help.** Proactively look for times when people might be stuck. 

***Stick to the essentials in tutorials.*** It’s fine to provide guidance for beginners, but education isn’t a substitute for great app design.

**Make learning fun and discoverable.** Learning by doing is a lot more fun and effective than reading a list of instructions. 

## 3. Loading

When content is loading, a blank or static screen can make it seem like your app is frozen, resulting in confusion and frustration, and potentially causing people to leave your app.

***Make it clear when loading is occurring.*** At minimum, show an activity spinner that communicates something is happening. 

**Show content as soon as possible.** Don’t make people wait for content to load before seeing the screen they're expecting. 

***Educate or entertain people to mask loading time.*** Consider showing hints about gameplay, entertaining video sequences, or interesting placeholder graphics.

**Customize loading screens.** Although standard progress indicators are usually OK, they can sometimes feel out of context. 

## 4. Navigation

People tend to be unaware of an app’s navigation until it doesn’t meet their expectations. Your job is to implement navigation in a way that supports the structure and purpose of your app without calling attention to itself.

**Hierarchical Navigation** Make one choice per screen until you reach a destination. To go to another destination, you must retrace your steps or start over from the beginning and make different choices. Settings and Mail use this navigation style.

**Flat Navigation** Switch between multiple content categories. Music and App Store use this navigation style.

**Content-Driven or Experience-Driven Navigation** Move freely through content, or the content itself defines the navigation. Games, books, and other immersive apps generally use this navigation style.

Some apps combine multiple navigation styles. For example, an app that uses flat navigation may implement hierarchical navigation within each category.

## References

More information on Markdown can be found at the following links:

- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)

