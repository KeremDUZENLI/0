## 🔁 Dual Boot Management

To manage boot entries (requires **Administrator Command Prompt**):

```cmd
bcdedit /export c:\bcdbackup
bcdedit /v
bcdedit /delete {identifier}
````

---

## 🌐 Change Display Language in Windows 10

1. **Via GUI:**

   * Go to:
     `Settings > Time & Language > Language`
   * Click on **Add a language** and install your preferred language.

2. **Via Command Line (Advanced):**

   * Press `Win + R`, type `cmd`, then press `Ctrl + Shift + Enter` to open **Command Prompt as Administrator**.
   * Run the following to list installed packages:

     ```cmd
     dism /online /get-packages
     ```
   * Look for packages starting with:

     ```
     Microsoft-Windows-Client-LanguagePack-Package
     ```

     * Example: `tr-TR` = Turkish, `en-US` = English (United States)
     * The language code is visible after the `~` character.

3. **Registry Language ID Change:**

   * Press `Win + R`, type `regedit` and hit Enter.
   * Navigate to:

     ```
     HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\Language
     ```
   * Look for the `Default` entry:

     * Double-click it and change the value to the **Language ID** of your desired language.
     * Example:

       * `en-US` → `0409`
       * `pt-BR` → `0416`
   * Optionally update the `InstallLanguage` field with the same ID if needed.
   * Save and **restart** the system.

---

## 🖱️ Restore Classic Right-Click Menu (Windows 11)

1. Press `Win + R`, type `regedit`, press Enter.
2. Navigate to:

   ```
   HKEY_CURRENT_USER\SOFTWARE\CLASSES\CLSID
   ```
3. Right-click the **CLSID** key:

   * Select `New > Key`, name it:

     ```
     {86ca1aa0-34aa-4e8b-a509-50c905bae2a2}
     ```
4. Inside that key:

   * Right-click it, choose `New > Key`, name it:

     ```
     InprocServer32
     ```
5. Double-click the default value in `InprocServer32`, and **leave it blank**.
6. Close Registry Editor and **restart** the system.

---

## 🌙 Turn Off Hibernate

Run the following in **Administrator Command Prompt**:

```cmd
powercfg -h off
```