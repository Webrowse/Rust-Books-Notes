# Chapter 05: Project Structure

This chapter is about: organize your file, manage dependencies and handle versioning.

## 1. Feature

Features are build flags that allow you to customize your project by adding optional functionality.  
- **Additive Rule**: Features should only add functionality, never remove it. This is because if two different parts of a project need different features from the same library, Cargo will enable the union (all of them) for that library.  
- **Optional Dependencies**: You can make a dependency optional in your Cargo.toml. When you do this, Cargo automatically creates a feature with the same name as that dependency.   
- **Conditional Compilation**: You use the #[cfg(feature = "...")] attribute to tell the compiler to only include specific code if a certain feature is turned on.  

## 2. Workspaces
As a project grows, it can become slow to compile because Rust treats each crate as one big unit,. Workspaces allow you to split a large project into smaller subcrates.  

- **Shared Resources**: All subcrates in a workspace share a single Cargo.lock file and a single output folder, making it easier to keep versions in sync.  
- **Convenience**: You can run commands like cargo test from the root of the workspace to test every subcrate at once.  
- **Internal Dependencies**: Subcrates can depend on each other using path dependencies (e.g., path = "../subcrate_name").  

## 3. Project Configuration

- **Metadata**: Your Cargo.toml should include information like the README path, keywords, and categories to help others find and understand your crate on crates.io.  
- **Patches**: The [patch] section lets you temporarily override a dependency with a different source (like a local folder or a specific Git branch). This is useful for testing a bug fix in a library your project uses.  

- **Profiles**: You can use profiles to trade off runtime performance for compile speed. For example, opt-level = 3 makes the program run as fast as possible but takes longer to compile, while lto (Link-Time Optimization) can give a performance boost by optimizing across the whole project.  

## 4. Conditional Compilation

Beyond features, you can use #[cfg] to write code that only runs on certain Operating Systems (like Windows vs. Linux) or specific CPU architectures (like x86 vs. ARM).  

## 5. Versioning

- **Semantic Versioning (SemVer)**: Rust follows the rule that major versions are for breaking changes, minor versions are for new features, and patch versions are for bug fixes.  
- **MSRV (Minimum Supported Rust Version)**: This is the oldest version of the Rust compiler that can build your project. It is good practice to increase the minor version of your crate if you decide to require a newer version of Rust.  
- **Minimal Versions**: To ensure your project doesn't accidentally depend on a feature from a newer version of a library than you actually listed, you can test with -Zminimal-versions, which forces Cargo to use the oldest allowed version of every dependency.