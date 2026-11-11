# WPF小作家 (WpfApp1) — 專案說明與實作步驟筆記

本專案為使用 **C#** 與 **WPF (.NET 8.0)** 開發的多視窗文書編輯器應用程式（「**WPF小作家**」）。透過主視窗可開啟獨立的文書編輯視窗（`MyDocumentViewer`），並整合了工具列（`ToolBarTray` / `ToolBar`）、狀態列（`StatusBar`）、富文本編輯器（`RichTextBox`）、WPF 內建命令綁定（`ApplicationCommands`）以及第三方套件 **Extended WPF Toolkit** 的色彩選擇器（`ColorPicker`）。

---

## 一、開發環境與專案規格

| 項目 | 規格 / 版本 |
| :--- | :--- |
| **開發框架** | .NET 8.0 (`net8.0-windows`) |
| **UI 框架** | Windows Presentation Foundation (WPF) |
| **第三方 NuGet 套件** | `Extended.Wpf.Toolkit` (Version `5.1.2`) |
| **主要視窗** | `MainWindow.xaml`（啟動首頁）、`MyDocumentViewer.xaml`（文件編輯視窗） |

---

## 二、專案目錄結構

```text
WpfApp1/
├── WpfApp1.sln                  # Visual Studio 解決方案檔
└── WpfApp1/                     # 專案主目錄 (Git 儲存庫根目錄)
    ├── WpfApp1.csproj           # 專案設定檔（含 NuGet 套件與圖片資源設定）
    ├── App.xaml                 # 應用程式進入點設定（StartupUri="MainWindow.xaml"）
    ├── App.xaml.cs              # 應用程式後端邏輯
    ├── AssemblyInfo.cs          # 組件資訊設定
    ├── MainWindow.xaml          # 主視窗介面（「WPF小作家」首頁）
    ├── MainWindow.xaml.cs       # 主視窗後端邏輯（點擊按鈕開啟編輯視窗）
    ├── MyDocumentViewer.xaml    # 文件編輯器視窗介面（工具列、狀態列、RichTextBox）
    ├── MyDocumentViewer.xaml.cs # 文件編輯器後端邏輯（處理 New / Open / Save 命令）
    ├── bold.png                 # 粗體按鈕圖示 (Resource)
    ├── italic.png               # 斜體按鈕圖示 (Resource)
    ├── underline.png            # 底線按鈕圖示 (Resource)
    ├── new.png                  # 新增文件按鈕圖示 (Resource)
    ├── open.png                 # 開啟文件按鈕圖示 (Resource)
    ├── save.png                 # 儲存文件按鈕圖示 (Resource)
    ├── trash.png                # 清除/垃圾桶按鈕圖示 (Resource)
    └── img/                     # 圖示備份資料夾
        ├── bold.png
        ├── italic.png
        ├── new.png
        ├── open.png
        ├── save.png
        ├── trash.png
        └── underline.png
```

---

## 三、視窗介面與功能概覽

### 1. 主視窗 (`MainWindow`)
- **視窗標題**：`WPF小作家`（寬 `800`、高 `450`）
- **介面元件**：配置一個 `OpenButton` 按鈕（顯示文字「**開啟新文件**」，字體大小 `22`）。
- **互動行為**：點擊「開啟新文件」按鈕時，建立一個新的 `MyDocumentViewer` 視窗實例並呼叫 `.Show()` 顯示，支援同時開啟多個編輯視窗。

### 2. 文件編輯器視窗 (`MyDocumentViewer`)
- **視窗標題**：`MyDocumentViewer`（寬 `800`、高 `450`）
- **版面配置 (`DockPanel`)**：
  1. **上方工具列區 (`ToolBarTray`, `DockPanel.Dock="Top"`, 背景色 `Aqua`)**：
     - **應用程式工具列 (`ApplicationToolbar`, 背景色 `Beige`)**：
       | 圖示 | 元件名稱 | 類型 | 綁定命令 (`Command`) | 功能說明 |
       | :---: | :--- | :--- | :--- | :--- |
       | ![new](new.png) | `NewButton` | `Button` | `ApplicationCommands.New` | 開啟新的 `MyDocumentViewer` 視窗 |
       | ![open](open.png) | `OpenButton` | `Button` | `ApplicationCommands.Open` | 開啟舊檔命令綁定 |
       | ![save](save.png) | `SaveButton` | `Button` | `ApplicationCommands.Save` | 儲存檔案命令綁定 |
       | ![trash](trash.png) | `TrashButton` | `Button` | 無 | 清除內容按鈕 |
     - **編輯工具列 (`EditingToolbar`, 背景色 `#FFEEDDAC`)**：
       | 圖示 / 外觀 | 元件名稱 | 類型 | 尺寸 (`Width` × `Height`) | 功能說明 |
       | :---: | :--- | :--- | :--- | :--- |
       | ![bold](bold.png) | `BoldButton` | `ToggleButton` | `38` × `38` | 粗體切換按鈕 |
       | ![italic](italic.png) | `ItalicButton` | `ToggleButton` | `38` × `38` | 斜體切換按鈕 |
       | ![underline](underline.png) | `UnderlineButton` | `ToggleButton` | `38` × `38` | 底線切換按鈕 |
       | 🎨 ColorPicker | `FontColorPicker` | `xctk:ColorPicker` | `160` × `38` | 字型顏色選擇器 |
       | 下拉選單 | `FontFamilyComboBox` | `ComboBox` | `150` × `38` | 字型家族選擇下拉選單 |
       | 下拉選單 | `FontSizeComboBox` | `ComboBox` | `50` × `38` | 字體大小選擇下拉選單 |
  2. **下方狀態列 (`StatusBar`, `DockPanel.Dock="Bottom"`, 背景色 `LightGray`)**：高度 `30`，停靠於視窗底部。
  3. **中央編輯區 (`RichTextBox`, 背景色 `#FFEFEFEC`)**：名稱為 `MainRichTextBox`，內含 `<FlowDocument/>`，自動填滿 `DockPanel` 剩餘空間。

---

## 四、從零開始的實作步驟教學

### 步驟 1：建立 WPF 專案與安裝 NuGet 套件
1. 在 Visual Studio 建立 **WPF 應用程式**，專案命名為 `WpfApp1`，目標框架選擇 **.NET 8.0**。
2. 開啟 **NuGet 套件管理員**，搜尋並安裝 `Extended.Wpf.Toolkit`（版本 `5.1.2`），以便在 XAML 中使用 `ColorPicker` 控制項。

### 步驟 2：匯入圖片資源 (`Resource`)
1. 將 7 張圖示檔（`bold.png`、`italic.png`、`new.png`、`open.png`、`save.png`、`trash.png`、`underline.png`）加入專案根目錄。
2. 在「方案總管」選取所有圖片，將屬性中的 **建置動作 (Build Action)** 設定為 **`Resource`**。

`WpfApp1.csproj` 設定檔內容如下：
```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net8.0-windows</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <UseWPF>true</UseWPF>
  </PropertyGroup>

  <ItemGroup>
    <None Remove="bold.png" />
    <None Remove="italic.png" />
    <None Remove="new.png" />
    <None Remove="open.png" />
    <None Remove="save.png" />
    <None Remove="trash.png" />
    <None Remove="underline.png" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Extended.Wpf.Toolkit" Version="5.1.2" />
  </ItemGroup>

  <ItemGroup>
    <Resource Include="bold.png" />
    <Resource Include="italic.png" />
    <Resource Include="new.png" />
    <Resource Include="open.png" />
    <Resource Include="save.png" />
    <Resource Include="trash.png" />
    <Resource Include="underline.png" />
  </ItemGroup>

</Project>
```

---

### 步驟 3：設計主視窗 (`MainWindow.xaml` & `MainWindow.xaml.cs`)
1. 將主視窗 `Title` 設為 `"WPF小作家"`。
2. 在 `Grid` 中加入一個 `Button`，命名為 `OpenButton`，內容設為 `"開啟新文件"`，並綁定 `Click="OpenButton_Click"` 事件。

#### `MainWindow.xaml`
```xml
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"
        Title="WPF小作家" Height="450" Width="800">
    <Grid>
        <Button x:Name="OpenButton" Content="開啟新文件" HorizontalAlignment="Left" Margin="279,122,0,0" VerticalAlignment="Top" Height="95" Width="213" FontSize="22" Click="OpenButton_Click"/>
    </Grid>
</Window>
```

#### `MainWindow.xaml.cs`
在 `OpenButton_Click` 事件處理常式中，實例化 `MyDocumentViewer` 並顯示視窗：
```csharp
using System.Text;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;

namespace WpfApp1
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void OpenButton_Click(object sender, RoutedEventArgs e)
        {
            MyDocumentViewer myDocumentViewer = new MyDocumentViewer();
            myDocumentViewer.Show();
        }
    }
}
```

---

### 步驟 4：建立文件編輯視窗 (`MyDocumentViewer.xaml` & `MyDocumentViewer.xaml.cs`)
1. 在專案中新增一個 **WPF 視窗**，命名為 `MyDocumentViewer.xaml`。
2. 在 `<Window>` 標籤內引入 Extended WPF Toolkit 命名空間：
   ```xml
   xmlns:xctk="http://schemas.xceed.com/wpf/xaml/toolkit"
   ```
3. 設定 `<Window.CommandBindings>`，綁定 `ApplicationCommands.New`、`ApplicationCommands.Open` 與 `ApplicationCommands.Save`。
4. 將預設的 `<Grid>` 改為 `<DockPanel>`：
   - 頂部放置 `<ToolBarTray DockPanel.Dock="Top">`，內含 `ApplicationToolbar` 與 `EditingToolbar` 兩個 `<ToolBar>`。
   - 底部放置 `<StatusBar DockPanel.Dock="Bottom">`。
   - 中央放置 `<RichTextBox x:Name="MainRichTextBox">`，並於內部加入 `<FlowDocument/>`。

#### `MyDocumentViewer.xaml`
```xml
<Window x:Class="WpfApp1.MyDocumentViewer"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        xmlns:xctk="http://schemas.xceed.com/wpf/xaml/toolkit"
        mc:Ignorable="d"
        Title="MyDocumentViewer" Height="450" Width="800">
    <Window.CommandBindings>
        <CommandBinding Command="ApplicationCommands.New" Executed="NewCommand_Executed"/>
        <CommandBinding Command="ApplicationCommands.Open" Executed="OpenCommand_Executed"/>
        <CommandBinding Command="ApplicationCommands.Save" Executed="SaveCommand_Executed"/>
    </Window.CommandBindings>
    <DockPanel>
        <ToolBarTray Height="40" VerticalAlignment="Top" HorizontalAlignment="Left" DockPanel.Dock="Top" Background="Aqua">
            <ToolBar x:Name="ApplicationToolbar" Height="40" Width="184" Background="Beige">
                <Button x:Name="NewButton" Height="38" Width="38" Command="ApplicationCommands.New">
                    <Image Height="32" Width="32" Source="/new.png"/>
                </Button>
                <Button x:Name="OpenButton" Height="38" Width="38" Command="ApplicationCommands.Open">
                    <Image Height="32" Width="32" Source="/open.png"/>
                </Button>
                <Button x:Name="SaveButton" Height="38" Width="38" Command="ApplicationCommands.Save">
                    <Image Height="32" Width="32" Source="/save.png"/>
                </Button>
                <Button x:Name="TrashButton" Height="38" Width="38" >
                    <Image Height="32" Width="32" Source="/trash.png"/>
                </Button>
            </ToolBar>
            <ToolBar x:Name="EditingToolbar" Height="40" Width="612" Background="#FFEEDDAC">
                <ToggleButton x:Name="BoldButton" Height="38" Width="38">
                    <Image Height="32" Width="32" Source="/bold.png" />
                </ToggleButton>
                <ToggleButton x:Name="ItalicButton" Height="38" Width="38">
                    <Image Height="32" Width="32" Source="/italic.png" />
                </ToggleButton>
                <ToggleButton x:Name="UnderlineButton" Height="38" Width="38">
                    <Image Height="32" Width="32" Source="/underline.png" />
                </ToggleButton>
                <xctk:ColorPicker x:Name="FontColorPicker" Width="160" Height="38"/>
                <ComboBox x:Name="FontFamilyComboBox" HorizontalAlignment="Left" VerticalAlignment="Center" Width="
                          150" Height="38" Margin="5,0,0,0"/>
                <ComboBox x:Name="FontSizeComboBox" HorizontalAlignment="Left" VerticalAlignment="Center" Width="
                          50" Height="38" Margin="5,0,0,0"/>
            </ToolBar>
        </ToolBarTray>
        <StatusBar Height="30" DockPanel.Dock="Bottom" Background="LightGray"/>
        <RichTextBox x:Name="MainRichTextBox" Background="#FFEFEFEC">
            <FlowDocument/>
        </RichTextBox>
    </DockPanel>
</Window>
```

#### `MyDocumentViewer.xaml.cs`
實作 `NewCommand_Executed`、`OpenCommand_Executed` 與 `SaveCommand_Executed` 事件處理常式：
```csharp
using System.Windows;
using System.Windows.Input;

namespace WpfApp1
{
    /// <summary>
    /// MyDocumentViewer.xaml 的互動邏輯
    /// </summary>
    public partial class MyDocumentViewer : Window
    {
        public MyDocumentViewer()
        {
            InitializeComponent();
        }

        private void NewCommand_Executed(object sender, ExecutedRoutedEventArgs e)
        {
            MyDocumentViewer newWindow = new MyDocumentViewer();
            newWindow.Show();
        }

        private void OpenCommand_Executed(object sender, ExecutedRoutedEventArgs e)
        {

        }

        private void SaveCommand_Executed(object sender, ExecutedRoutedEventArgs e)
        {

        }
    }
}
```

---

## 五、重點觀念整理

1. **多視窗開啟 (`Window.Show()`)**：
   - 在 `MainWindow` 點擊「開啟新文件」或在 `MyDocumentViewer` 觸發 `ApplicationCommands.New`（點擊新增按鈕或按快捷鍵 `Ctrl + N`）時，皆透過 `new MyDocumentViewer().Show()` 建立非強制回應（Non-modal）的新視窗。
2. **`DockPanel` 停靠佈局順序**：
   - `DockPanel` 具有 `LastChildFill="True"` 的預設特性，因此需先放置停靠在頂部的 `ToolBarTray`（`DockPanel.Dock="Top"`）與底部的 `StatusBar`（`DockPanel.Dock="Bottom"`），最後放置的 `RichTextBox` 便會自動填滿中間剩餘的所有空間。
3. **WPF 內建命令 (`ApplicationCommands`)**：
   - 使用 `<Window.CommandBindings>` 將 `ApplicationCommands.New`、`Open`、`Save` 綁定至後端方法，不僅能讓 `Button` 透過 `Command="..."` 觸發，也能自動支援系統預設快捷鍵（如 `Ctrl+N`、`Ctrl+O`、`Ctrl+S`）。
