---
title: "Easy Steps to Create a Maui Blazor Hybrid App Using VS Code"
datePublished: Sat Dec 06 2025 16:12:25 GMT+0000 (Coordinated Universal Time)
cuid: cmiuhs6p1000502l8gpz34odr
slug: easy-steps-to-create-a-maui-blazor-hybrid-app-using-vs-code
tags: csharp, blazor, maui

---

This guide explains how to set up and run .NET MAUI apps on **Windows** and **macOS** using **Visual Studio Code**.

---

## Windows Setup

1. Install **Visual Studio Code**
    
2. Install **.NET SDK 9+**
    
3. Install the **.NET MAUI extension** in VS Code
    
4. Install the MAUI workload:
    
    ```sh
    dotnet workload install maui
    ```
    
5. Create a new MAUI Blazor project:
    
    ```plaintext
    dotnet new maui-blazor -n YourAppName
    ```
    
6. List available workloads:
    
    ```plaintext
    dotnet workload search
    ```
    
7. Run the app on Windows:
    
    ```plaintext
    dotnet build -t:Run -f net9.0-windows10.0.19041.0
    ```
    
    ## macOS Setup
    
    1. Install **Visual Studio Code**
        
    2. Install **.NET SDK 9+**
        
    3. Install the **.NET MAUI extension** in VS Code
        
    4. Install **Xcode** from the App Store
        
    5. Install the MAUI workload:
        
        ```plaintext
        dotnet workload install maui
        ```
        
    6. Create a new MAUI Blazor project:
        
        ```plaintext
        dotnet new maui-blazor -n YourAppName
        ```
        
    7. List available workloads:
        
        ```plaintext
        dotnet workload search
        ```
        
    8. Run the app on MacCatalyst:
        
        ```plaintext
        dotnet build -t:Run -f net9.0-maccatalyst
        ```
        
    
    ---
    
    ## Notes
    
    * VS Code supports **building and running**, but not full drag-and-drop MAUI UI design.
        
    * Xcode is required on macOS for **MacCatalyst** and **iOS** builds.
        
    * Use `.NET 9` or later for MAUI `.net9.0-*` targets.