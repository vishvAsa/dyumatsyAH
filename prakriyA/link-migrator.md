+++
title = "Link migrator"
+++

Paste an old Dyugangā-group URL below to get its post-move location. Handles `sanskrit.github.io/groups/dyuganga/…` links as well as pre-move `dyugangaa` links (query strings are preserved).

<textarea id="migInput" rows="3" style="width:100%" placeholder="https://sanskrit.github.io/groups/dyuganga/projects/text/git-workflow/4_sending-pull-request/?githubUserId=XYZ&repo=REPO"></textarea>

<button id="migBtn" type="button">Convert</button> <span id="migNote"></span>

<textarea id="migOutput" rows="3" readonly style="width:100%" placeholder="Converted URL appears here"></textarea>

<p><a id="migLink" href="#" target="_blank" rel="noopener"></a></p>
<button id="migCopy" type="button">Copy</button>

<script>
function migrateUrl(input) {
  var u = String(input || "").replace(/[\s ]+/g, "");
  if (!u) return { url: "", moved: false, note: "Enter a URL above." };
  u = u.replace(/dyuganga\/\//g, "dyuganga/");
  var expanded = false;
  if (/^\/groups\/dyuganga\//.test(u)) { u = "https://sanskrit.github.io" + u; expanded = true; }
  else if (/^\/dyugangaa\//.test(u)) { u = "https://vishvAsa.github.io" + u; expanded = true; }
  else if (/^\/dyumatsyAH\//.test(u)) { u = "https://vishvAsa.github.io" + u; expanded = true; }
  var rules = [
    [/^https?:\/\/sanskrit\.github\.io\/groups\/dyuganga\//i, "https://vishvAsa.github.io/dyugangaa/"],
    [/^https?:\/\/vishvasa\.github\.io\/dyugangaa\/internal\//i, "https://vishvAsa.github.io/dyumatsyAH/"],
    [/^https?:\/\/vishvasa\.github\.io\/dyugangaa\/projects\/text\/git-workflow\//i, "https://vishvAsa.github.io/dyumatsyAH/prakriyA/git-workflow/"],
    [/^https?:\/\/vishvasa\.github\.io\/dyugangaa\/projects\/text\/proofreading\/(kalpa-texts|kAvya-texts|tattva-texts)\//i, "https://vishvAsa.github.io/dyugangaa/projects/text/searchable-text/$1/"],
    [/^https?:\/\/vishvasa\.github\.io\/dyugangaa\/projects\/text\/proofreading\//i, "https://vishvAsa.github.io/dyumatsyAH/prakriyA/proofreading/"]
  ];
  var moved = false, guard = 0, again = true, i;
  while (again && guard++ < 6) {
    again = false;
    for (i = 0; i < rules.length; i++) {
      if (rules[i][0].test(u)) { u = u.replace(rules[i][0], rules[i][1]); moved = true; again = true; break; }
    }
  }
  var note = moved ? "Converted to post-move URL."
    : (expanded ? "Location unchanged - expanded to full URL."
    : "No mapping applies - returned unchanged.");
  return { url: u, moved: moved, note: note };
}
if (typeof document !== "undefined") {
  (function () {
    var inp = document.getElementById("migInput"),
      out = document.getElementById("migOutput"),
      link = document.getElementById("migLink"),
      note = document.getElementById("migNote");
    function run() {
      var r = migrateUrl(inp.value);
      out.value = r.url;
      note.textContent = r.note;
      if (r.url) { link.href = r.url; link.textContent = "Open converted URL"; }
      else { link.removeAttribute("href"); link.textContent = ""; }
    }
    document.getElementById("migBtn").addEventListener("click", run);
    inp.addEventListener("input", run);
    document.getElementById("migCopy").addEventListener("click", function () {
      out.select();
      try { document.execCommand("copy"); } catch (e) {
        if (navigator.clipboard) navigator.clipboard.writeText(out.value);
      }
    });
  })();
}
</script>
