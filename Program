using System;
using System.Collections.Generic;
using System.Drawing;
using System.Drawing.Drawing2D;
using System.Windows.Forms;

class Toggle : Control
{
    public bool On;
    public event EventHandler Changed;
    public Toggle() { Size = new Size(40, 20); DoubleBuffered = true; Cursor = Cursors.Hand; }
    protected override void OnPaint(PaintEventArgs e)
    {
        var g = e.Graphics; g.SmoothingMode = SmoothingMode.AntiAlias;
        using (var b = new SolidBrush(On ? Color.FromArgb(123, 43, 255) : Color.FromArgb(42, 32, 48)))
        { g.FillEllipse(b, 0, 0, 20, 20); g.FillEllipse(b, 20, 0, 20, 20); g.FillRectangle(b, 10, 0, 20, 20); }
        using (var b = new SolidBrush(On ? Color.White : Color.FromArgb(154, 138, 163)))
            g.FillEllipse(b, On ? 22 : 4, 3, 14, 14);
    }
    protected override void OnClick(EventArgs e) { On = !On; Invalidate(); Changed?.Invoke(this, e); base.OnClick(e); }
}

class MenuForm : Form
{
    static readonly Color Bg = Color.FromArgb(21, 14, 25), Dim = Color.FromArgb(154, 138, 163),
        Txt = Color.FromArgb(239, 230, 243), Neon = Color.FromArgb(255, 43, 214);

    // kind, label, min, max, start, unit
    static readonly Dictionary<string, object[][]> Tabs = new Dictionary<string, object[][]>
    {
        ["Player"] = new[] { new object[] { "t", "Infinite stamina" }, new object[] { "t", "Ghost mode" }, new object[] { "t", "Night vision" },
            new object[] { "s", "Move speed", 1.0, 5.0, 1.0, "x" }, new object[] { "s", "Jump height", 1.0, 4.0, 1.0, "x" } },
        ["World"] = new[] { new object[] { "t", "Always daytime" }, new object[] { "t", "Clear weather" },
            new object[] { "s", "Time of day", 0.0, 24.0, 12.0, "h" }, new object[] { "s", "Gravity", 0.0, 3.0, 1.0, "x" } },
        ["Vehicles"] = new[] { new object[] { "t", "Unbreakable body" }, new object[] { "t", "Infinite fuel" },
            new object[] { "s", "Top speed", 1.0, 3.0, 1.0, "x" }, new object[] { "s", "Grip", 1.0, 3.0, 1.0, "x" } },
        ["Settings"] = new[] { new object[] { "t", "Always on top" }, new object[] { "s", "Window opacity", 40.0, 100.0, 100.0, "%" } },
    };

    readonly Panel nav = new Panel { Dock = DockStyle.Left, Width = 150, BackColor = Color.FromArgb(16, 10, 20), Padding = new Padding(8) };
    readonly Panel page = new Panel { Dock = DockStyle.Fill, Padding = new Padding(18, 12, 18, 0), BackColor = Bg };
    readonly Label status = new Label { Dock = DockStyle.Bottom, Height = 28, ForeColor = Dim, BackColor = Color.FromArgb(16, 10, 20),
        Text = "  Mockup only. Nothing is connected to any game. Insert hides the window.", TextAlign = ContentAlignment.MiddleLeft };
    readonly Dictionary<string, object> state = new Dictionary<string, object>();
    string cur = "Player";

    public MenuForm()
    {
        Text = "Dasher Menu"; ClientSize = new Size(760, 420); BackColor = Bg; ForeColor = Txt;
        Font = new Font("Segoe UI", 10f); FormBorderStyle = FormBorderStyle.FixedSingle; MaximizeBox = false;
        StartPosition = FormStartPosition.CenterScreen; KeyPreview = true;
        Controls.Add(page); Controls.Add(nav); Controls.Add(status);
        KeyDown += (s, e) => { if (e.KeyCode == Keys.Insert) WindowState = FormWindowState.Minimized; };
        DrawNav(); DrawPage();
    }

    void DrawNav()
    {
        nav.Controls.Clear(); int y = 8;
        foreach (var name in Tabs.Keys)
        {
            var b = new Button { Text = name, Left = 8, Top = y, Width = 134, Height = 34, FlatStyle = FlatStyle.Flat, TextAlign = ContentAlignment.MiddleLeft,
                ForeColor = name == cur ? Txt : Dim, BackColor = name == cur ? Color.FromArgb(70, 20, 80) : Color.FromArgb(16, 10, 20), Cursor = Cursors.Hand };
            b.FlatAppearance.BorderSize = 0;
            string n = name; b.Click += (s, e) => { cur = n; DrawNav(); DrawPage(); };
            nav.Controls.Add(b); y += 40;
        }
    }

    void DrawPage()
    {
        page.Controls.Clear(); int y = 6;
        for (int i = 0; i < Tabs[cur].Length; i++)
        {
            var it = Tabs[cur][i]; string key = cur + i, label = (string)it[1];
            page.Controls.Add(new Label { Text = label, Left = 0, Top = y + 4, Width = 220, AutoSize = false, ForeColor = Txt });
            if ((string)it[0] == "t")
            {
                var t = new Toggle { Left = 540, Top = y + 4, Anchor = AnchorStyles.Top | AnchorStyles.Right, On = state.ContainsKey(key) && (bool)state[key] };
                t.Changed += (s, e) => { state[key] = t.On; status.Text = "  " + label + ": " + (t.On ? "on" : "off") + " (demo)";
                    if (label == "Always on top") TopMost = t.On; };
                page.Controls.Add(t);
            }
            else
            {
                double min = (double)it[2], max = (double)it[3], v = state.ContainsKey(key) ? (double)state[key] : (double)it[4]; string unit = (string)it[5];
                var val = new Label { Left = 500, Top = y + 4, Width = 70, TextAlign = ContentAlignment.MiddleRight, ForeColor = Neon, Text = v.ToString("0.0") + unit };
                var tb = new TrackBar { Left = 270, Top = y, Width = 220, Minimum = (int)(min * 10), Maximum = (int)(max * 10), Value = (int)(v * 10),
                    TickStyle = TickStyle.None, AutoSize = false, Height = 28 };
                tb.ValueChanged += (s, e) => { double nv = tb.Value / 10.0; state[key] = nv; val.Text = nv.ToString("0.0") + unit;
                    if (label == "Window opacity") Opacity = nv / 100.0; status.Text = "  " + label + ": " + val.Text + " (demo)"; };
                page.Controls.Add(tb); page.Controls.Add(val);
            }
            y += 40;
        }
    }
}

static class Program
{
    [STAThread] static void Main() { Application.EnableVisualStyles(); Application.Run(new MenuForm()); }
}
