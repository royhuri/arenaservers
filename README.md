private async void button3_Click(object sender, EventArgs e)
{
    try
    {
        // Read game path from INI
        string path = UpdateINI.ReadValue("Auth", "Path");
        string email = CrystalLauncherCore1.UserSession.email;
        string password = CrystalLauncherCore1.UserSession.password;

        if (string.IsNullOrWhiteSpace(path) || path == "NONE")
        {
            MessageBox.Show("אנא בחר תיקיית משחק.", "ZeroFN Launcher", MessageBoxButtons.OK, MessageBoxIcon.Warning);
            return;
        }

        string exePath = Path.Combine(path, @"FortniteGame\Binaries\Win64\FortniteClient-Win64-Shipping.exe");

        if (!File.Exists(exePath))
        {
            MessageBox.Show("הקובץ FortniteClient-Win64-Shipping.exe לא נמצא בנתיב שציינת.", "ZeroFN Launcher", MessageBoxButtons.OK, MessageBoxIcon.Error);
            return;
        }

        if (string.IsNullOrWhiteSpace(email) || string.IsNullOrWhiteSpace(password) || email == "NONE" || password == "NONE")
        {
            MessageBox.Show("חסרים פרטי התחברות.", "ZeroFN Launcher", MessageBoxButtons.OK, MessageBoxIcon.Error);
            return;
        }

        // ✅ Read game version from config (INI)
        string gameVersion = UpdateINI.ReadValue("Game", "Version");
        if (string.IsNullOrWhiteSpace(gameVersion))
            gameVersion = "UNKNOWN";

        // הורדת DLL והעתקה
        string dllUrl = "https://github.com/Dor-guri/sdasdasd/raw/refs/heads/main/Starfall.dll";
        string dllDest = Path.Combine(path, @"Engine\Binaries\ThirdParty\NVIDIA\NVaftermath\Win64", "GFSDK_Aftermath_Lib.x64.dll");

        using (WebClient web = new WebClient())
        {
            await web.DownloadFileTaskAsync(new Uri(dllUrl), dllDest);
        }

        // ✅ Add game version to the launch args
        string commonArgs = $"-epicapp=Fortnite -epicenv=Prod -epiclocale=en-us -epicportal " +
                            $"-noeac -fromfl=be -fltoken=h1cdhchd10150221h130eB56 -skippatchcheck " +
                            $"--gameversion={gameVersion}";

        // Start processes with version included
        PSBasics.Start(path, commonArgs, email, password);
        FakeAC.Start(path, "FortniteClient-Win64-Shipping_BE.exe", commonArgs, "r");
        FakeAC.Start(path, "FortniteLauncher.exe", commonArgs, "dsf");
    }
    catch (Exception ex)
    {
        MessageBox.Show("שגיאה בהפעלת המשחק: " + ex.Message, "ZeroFN Launcher", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}
