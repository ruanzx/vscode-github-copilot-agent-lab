# 🎤 Speaker Notes — GitHub Dev Days: VS Code Copilot Agent Lab

> **Ngôn ngữ:** Tiếng Việt  
> **Thời lượng tổng:** ~1 giờ  
> **Đối tượng:** Developer, cấp độ trung cấp  
> **Stack:** Python / FastAPI / Jinja2 / HTMX

---

## 🧭 Cấu trúc buổi lab

| Phần | Nội dung | Thời gian |
|------|----------|-----------|
| [Giới thiệu](#giới-thiệu) | Tổng quan workshop | 5 phút |
| [Chuẩn bị trước lab](#trước-lab--yêu-cầu-chuẩn-bị) | Checklist trước khi bắt đầu | — |
| [Phần 1](#phần-1-setup--context-engineering) | Setup & Context Engineering | 15 phút |
| [Phần 2](#phần-2-design-first-frontend) | Design-First Frontend | 15 phút |
| [Phần 3](#phần-3-custom-quiz-master) | Custom Quiz Master | 10 phút |
| [Phần 4](#phần-4-multi-agent-development) | Multi-Agent Development | 20 phút |
| [Kết thúc](#kết-thúc) | Tổng kết & Q&A | 5 phút |

---

## Giới thiệu

> **[Nói với khán giả]**

"Xin chào mọi người. Hôm nay chúng ta sẽ cùng thực hành một workshop có tên là **Soc Ops** — một ứng dụng Social Bingo được xây dựng bằng Python và FastAPI. Mục tiêu không phải là xây dựng lại từ đầu, mà là dùng **GitHub Copilot Agent Mode** trong VS Code để *cải tiến* và *mở rộng* ứng dụng này theo nhiều hướng khác nhau.

Trong vòng khoảng 1 tiếng, mọi người sẽ trải nghiệm được bốn kỹ năng cốt lõi khi làm việc với AI trong lập trình:

1. **Context Engineering** — Dạy AI hiểu codebase của mình
2. **Agentic Primitives** — Sử dụng background agent, cloud agent
3. **Design-First Development** — Thiết kế UI với sự hỗ trợ của AI
4. **Test-Driven Development** — Viết tính năng mới theo chu trình TDD

Hãy mở VS Code và cùng bắt đầu."

---

## Trước Lab — Yêu Cầu Chuẩn Bị

> **[Nhắc nhở khán giả kiểm tra trước khi vào phần 1]**

### Checklist bắt buộc

Yêu cầu mọi người kiểm tra các mục sau trước khi tiếp tục:

- [ ] **VS Code phiên bản v1.107 trở lên** — Vào Help > About để kiểm tra. Nếu có bản cập nhật, cập nhật ngay.
- [ ] **Đăng nhập GitHub Copilot** — Cần có gói Copilot Pro, Business, hoặc Enterprise
- [ ] **Git đã được cài đặt**
- [ ] **Python 3.13 và uv đã được cài đặt**
- [ ] **Chat panel của Copilot đang mở**

---

### ⚠️ Cài đặt GitHub MCP trong VS Code

> **[Điểm quan trọng — giải thích rõ cho khán giả]**

"Để dùng được các tính năng như Cloud Agent tích hợp với GitHub, chúng ta cần cài đặt **GitHub MCP (Model Context Protocol) extension** trong VS Code."

**Hướng dẫn:**
1. Mở VS Code
2. Vào **Extensions** (`Ctrl+Shift+X`)
3. Tìm kiếm `GitHub MCP` hoặc `MCP GitHub`
4. Cài đặt extension từ Microsoft/GitHub
5. Reload VS Code sau khi cài xong

> 💡 **Lưu ý cho speaker:** Nếu ai trong phòng chưa cài, giúp họ cài xong trước khi tiếp tục. Đây là prerequisite quan trọng cho các bước Cloud Agent ở phần sau.

---

### 🔐 Xác thực tài khoản GitHub với VS Code

> **[Giải thích để khán giả không bị kẹt ở bước này]**

"Tiếp theo, cần đảm bảo VS Code đã được kết nối với tài khoản GitHub của mình."

**Hướng dẫn:**
1. Ở góc dưới bên trái của VS Code, click vào **biểu tượng người dùng (Account icon)**
2. Chọn **"Sign in with GitHub"**
3. Trình duyệt sẽ mở ra — đăng nhập và xác nhận
4. Quay lại VS Code, kiểm tra biểu tượng account đã hiển thị tên tài khoản chưa

> 💡 **Nếu đã đăng nhập rồi:** Bỏ qua bước này. Nhưng hãy xác nhận bằng cách click vào Account icon để thấy email tài khoản hiện tại.

---

### ☁️ Kích hoạt Cloud Agent

> **[Giải thích chi tiết — đây là tính năng mới và nhiều người chưa biết]**

"Cloud Agent cho phép chúng ta chạy các tác vụ AI dài hơi *trên cloud*, không chiếm tài nguyên máy local. Agent sẽ làm việc độc lập, tạo branch, commit code, và cuối cùng mở Pull Request để mình review."

**Cách bật Cloud Agent:**
1. Mở **Chat panel** trong VS Code (Ctrl+Alt+I hoặc click icon Copilot)
2. Click vào dấu **`+`** góc trên của Chat panel
3. Trong menu thả xuống, chọn **"New cloud agent"**
4. Nếu lần đầu sử dụng, VS Code sẽ yêu cầu xác nhận quyền — chọn **Allow**

> ⚠️ **Lưu ý quan trọng về Cloud Agent:**
> 
> "Khi Cloud Agent chạy, nó sẽ tự động lấy thông tin repository của bạn. Đôi khi agent bị lỗi vì nó lấy **sai owner name** của repo. Ví dụ, nếu bạn fork từ repo gốc, agent có thể dùng tên owner của repo gốc thay vì tên GitHub của bạn.
>
> **Cách kiểm tra:** Sau khi khởi động Cloud Agent, hãy theo dõi output của nó. Nếu thấy url repo không đúng username của bạn — hủy và chạy lại, lần này paste rõ URL repo của bạn vào prompt."

---

## Phần 1: Setup & Context Engineering

> ⏱️ **Thời gian:** ~15 phút

---

### Bước 1.1 — Fork Repository

> **[Giải thích trước khi để khán giả tự làm]**

"Bước đầu tiên là lấy repo mẫu về tài khoản GitHub cá nhân."

> ⚠️ **Điểm chú ý quan trọng:** "Repo này **không có nút 'Use this template'** như một số template repo khác. Thay vào đó, hãy **Fork** repo về tài khoản của mình."

**Hướng dẫn:**
1. Truy cập: [github.com/madebygps/vscode-github-copilot-agent-lab](https://github.com/madebygps/vscode-github-copilot-agent-lab)
2. Click nút **Fork** (góc trên phải)
3. Đặt tên repository: `my-soc-ops-python` (hoặc tên tùy ý)
4. Chọn **Public**
5. Click **Create fork**

---

### Bước 1.2 — Kích hoạt GitHub Pages

"Ứng dụng của chúng ta sẽ được deploy tự động lên GitHub Pages mỗi khi push code. Để bật tính năng này:"

1. Vào **Settings** của repo vừa fork
2. Chọn **Pages** từ menu bên trái
3. Trong mục **"Build and deployment"**, chọn source là **GitHub Actions**
4. Save lại

> ✅ Sau mỗi lần commit, ứng dụng sẽ publish tại: `https://{username}.github.io/{repo-name}`

---

### Bước 1.3 — Clone và Mở trong VS Code

1. Mở VS Code
2. Nhấn `Ctrl+Shift+P` → gõ `Git: Clone` → chọn **Clone from GitHub**
3. Chọn repo vừa fork
4. Khi được hỏi về **recommended extensions** — chọn **Install**

---

### Bước 1.4 — Chạy Setup Agent

"Đây là bước đầu tiên thực sự dùng Copilot Agent. Gõ lệnh sau vào Chat panel:"

```
/setup
```

"Agent sẽ tự động:
- Phát hiện môi trường (Python, uv)
- Cài đặt dependencies còn thiếu
- Khởi động development server"

> ✅ **Thành công:** App chạy được tại `http://localhost:8000`

---

### Task 1.A — Tạo Workspace Instructions

> **[Giải thích tầm quan trọng của Context Engineering]**

"Context Engineering là kỹ thuật **dạy AI hiểu codebase của bạn**. Thay vì giải thích từng lần, bạn viết một file instructions một lần duy nhất, và tất cả các lần chat sau đó AI đều hiểu được ngữ cảnh của project.

Hãy để Copilot tự sinh ra instructions file:"

1. Nhấn `Ctrl+Shift+P` → gõ `Chat: Generate Workspace Instructions File`
2. Chờ agent phân tích codebase và sinh file
3. Đọc qua file được tạo — thường sẽ **quá chi tiết**
4. Tiếp tục trong Chat với prompt:
   ```
   Compress down by half and add a mandatory development checklist 
   (lint, build, test) to the top
   ```
5. Commit file instructions

> 💡 **Nói với khán giả:** "Từ giờ trở đi, mọi prompt bạn gửi đều tự động có context về project này. AI không còn mù tịt về stack, cấu trúc thư mục, hay conventions của codebase nữa."

---

### Task 1.B — Background Agent (Chạy Song Song)

> **[Giới thiệu khái niệm Background Agent]**

"Background Agent chạy trong một **git worktree riêng biệt** — tức là nó làm việc độc lập, không ảnh hưởng đến code đang mở của bạn. Lý tưởng cho các tác vụ không cần giám sát."

**Cách dùng:**
1. Click `+` trong Chat panel → chọn **New background agent**
2. Nhập prompt:
   ```
   Add linting rules with ruff for unused vars and type hints; fix any errors
   ```
3. Để agent chạy. Trong lúc đó, tiếp tục với các bước khác
4. Khi agent xong: **Review** → **Apply** các thay đổi
5. Chuột phải vào session → **Delete** để dọn sạch

---

### Task 1.C — Cloud Agent (README Improvement)

"Bây giờ thử Cloud Agent với một tác vụ nhẹ hơn:"

1. Click `+` → **New cloud agent**
2. Nhập:
   ```
   Make the README more engaging as a landing page to the project
   ```

> ⚠️ **Nhắc lại cảnh báo:** "Kiểm tra trong output của agent xem URL repo có đúng username của bạn không. Nếu sai — cancel và chạy lại."

3. Để agent chạy trong nền
4. Khi hoàn thành, agent sẽ **tạo Pull Request** — vào GitHub web để review và merge

> 💡 "Đây là điểm mạnh của Cloud Agent: nó làm việc async, tự tạo PR, bạn chỉ cần review và quyết định merge hay không."

---

### Task 1.D — Khám Phá Existing Instructions

"Repo đã có sẵn một số instruction files để giúp AI hiểu project tốt hơn."

**Hai file quan trọng:**

- **`.github/instructions/css-utilities.instructions.md`**  
  → Mô tả các CSS utility classes trong project. AI sẽ dùng các class này thay vì viết CSS duplication.

- **`.github/instructions/frontend-design.instructions.md`**  
  → Chứa ràng buộc thiết kế như "no purple gradients". Đây là cách bạn **định hướng thẩm mỹ** cho AI.

> 💡 **Câu hỏi cho khán giả:** "Bạn có thể thêm instruction gì để nudge AI ra khỏi những pattern nhàm chán thường gặp?"

---

## Phần 2: Design-First Frontend

> ⏱️ **Thời gian:** ~15 phút

---

### Task 2.1 — Thiết Kế Lại Toàn Bộ UI (Plan Mode)

> **[Giải thích Plan Mode]**

"**Plan Mode** là chế độ trong Copilot cho phép AI *đề xuất kế hoạch* trước khi thực hiện. Đây là best practice cho mọi tác vụ lớn — bạn review plan, điều chỉnh, rồi mới bấm Implement."

**Hướng dẫn:**
1. Trong Chat panel, toggle sang **Plan Mode** (nút ở phía dưới chat)
2. Nhập vision của bạn:
   ```
   Let's do a full redesign. Make it [CHỦ ĐỀ BẠN CHỌN]
   ```
3. Đọc kỹ plan được sinh ra
4. Hỏi thêm 2-3 lần để điều chỉnh plan cho đúng ý
5. Khi hài lòng → click **Implement**

**Một số chủ đề gợi ý:**

| Phong cách | Chủ đề |
|------------|--------|
| Tối giản | Minimalist Mono, Scandinavian Calm |
| Retro | Retro Terminal Green, Pixel Arcade |
| Dark | Cyberpunk Neon, Space Galaxy Glow |
| Vui nhộn | Playful Candy Pop, Anime Bubble |
| Chuyên nghiệp | Corporate Clean Blue, Gradient Glass |

> 💡 **Gợi ý khi trình bày:** Chọn trước một theme ấn tượng để demo live, ví dụ **Cyberpunk Neon** tạo hiệu ứng trực quan tốt cho khán giả.

---

### Task 2.2 — Cập Nhật Instructions Sau Redesign

"Sau khi đã redesign xong, hãy cập nhật lại instructions để AI nhớ design style này:"

```
Add a design guide section to copilot-instructions.md
```

Commit và push lên GitHub — kiểm tra GitHub Pages đã cập nhật chưa.

---

### Task 2.3 — Cloud Agents Song Song (Khám Phá Đa Dạng)

> **[Đây là điểm WOW của phần này]**

"Thay vì thử từng design một, hãy **chạy 3 Cloud Agent cùng lúc** để khám phá 3 hướng thiết kế khác nhau song song."

1. Mở Chat mới → chuyển sang **Plan Mode**
2. Nhập:
   ```
   Redesign the start screen as a more engaging landing page
   ```
3. Trong plan được đề xuất, chạy lệnh:
   ```
   /cloud-explore design variations
   ```
   *(Xem file `.github/prompts/cloud-explore.prompt.md` để hiểu prompt này làm gì)*

4. Theo dõi **Agent Sessions panel** — bạn sẽ thấy 3 cloud agents đang chạy song song
5. Click vào bất kỳ session nào để theo dõi tiến độ, hoặc mở trên web

> ✅ **Kết quả:** 3 design variations, mỗi cái chạy trên một branch riêng, tạo PR riêng để bạn so sánh và chọn.

> ⏰ "Các Cloud Agent này cần vài phút để chạy. Trong lúc chờ, chúng ta chuyển sang Phần 3."

---

## Phần 3: Custom Quiz Master

> ⏱️ **Thời gian:** ~10 phút

---

### Giới Thiệu Custom Agents

> **[Giải thích khái niệm]**

"**Custom Agents** là các workflow đặc biệt được định nghĩa trong folder `.github/agents/`. Mỗi agent có:
- **Specialized knowledge** — Hiểu biết sâu về một lĩnh vực cụ thể
- **Consistent workflow** — Quy trình lặp đi lặp lại nhất quán
- **Focused context** — Ngữ cảnh được thu hẹp lại để output tốt hơn

Hãy xem file `.github/agents/quiz-master.agent.md` để hiểu cách một custom agent được định nghĩa."

---

### Task 3.1 — Dùng Quiz Master Agent

1. Mở Chat mới
2. Ở **agent picker** (dropdown chọn agent), chọn **Quiz Master**
3. Nhập theme bạn muốn:
   ```
   Update questions to [CHỦ ĐỀ BẠN MUỐN]
   ```
   Hoặc đơn giản:
   ```
   Update quiz
   ```
4. Đọc các câu hỏi được sinh ra
5. Tinh chỉnh thêm:
   ```
   Make them more chaotic and unexpected!
   ```

**Một số chủ đề gợi ý:**

| Loại | Chủ đề |
|------|--------|
| Nghề nghiệp | Skill Bingo, Team Bingo, Work Culture |
| Cá nhân | Personality, Lifestyle, Travel |
| Tech | Tech Life, Dev Memes, Fandom |
| Vui | Chaos Bingo, Office Humor, Opposites |

---

### Task 3.2 — Quiz Master Chạy Như Cloud Agent

"Custom agents cũng có thể chạy trên cloud:"

1. Click `+` → **New cloud agent**
2. Chọn **Quiz Master** làm agent
3. Nhập:
   ```
   Create a Tech Life Bingo with questions about 
   coding habits, IDE preferences, and developer culture
   ```
4. Để chạy background — check PR khi xong

> 💡 "Đây là sự kết hợp giữa custom workflow và cloud execution — rất mạnh cho các tác vụ chuyên biệt."

---

## Phần 4: Multi-Agent Development

> ⏱️ **Thời gian:** ~20 phút

---

### Task 4.1 — Scavenger Hunt Mode (TDD)

> **[Giải thích TDD workflow với agents]**

"Bây giờ chúng ta sẽ xây dựng một tính năng mới hoàn toàn: **Scavenger Hunt Mode** — thay vì bingo grid, hiển thị câu hỏi dạng checklist với progress bar.

Chúng ta sẽ làm theo chu trình **Red → Green → Refactor** của TDD, nhưng dùng agents chuyên biệt cho từng phase."

---

#### Phase 1: Plan

1. Mở Chat mới → **Plan Mode**
2. Nhập:
   ```
   Add a new Scavenger Hunt mode: same questions, but shown as a 
   simple list with checkboxes and a progress meter
   ```
3. Review plan — kiểm tra plan có:
   - ✅ Thêm mode vào start screen
   - ✅ Tạo component mới cho list view
   - ✅ Có progress indicator
   - ❌ Không thêm quá nhiều tính năng thừa
4. Điều chỉnh plan 2-3 lần nếu cần

---

#### Phase 2: TDD Red — Viết Test Thất Bại

1. Chọn agent **TDD Red**
2. Click **Start**
3. Agent sẽ viết tests cho:
   - Component rendering
   - Checkbox interactions
   - Progress calculation
   - State management
4. Mở **Test Explorer** trong VS Code để thấy các test đang fail (màu đỏ)

> 💡 "Tests fail là đúng — chúng ta chưa implement gì cả. Đây chính là điểm khởi đầu của TDD."

---

#### Phase 3: TDD Green — Pass Các Tests

1. Chọn agent **TDD Green**
2. Agent sẽ:
   - Implement code tối thiểu để pass tests
   - Chạy tests sau mỗi thay đổi
   - Lặp đến khi tất cả tests xanh

---

#### Phase 4: Refactor — Dọn Sạch Code

1. Chọn agent **TDD Refactor**
2. Agent dọn sạch code trong khi giữ nguyên tests xanh

> 💡 **Bonus:** Dùng **TDD Supervisor** để tự động hóa toàn bộ Red → Green → Refactor flow.

---

#### ⚠️ Xử Lý Khi Có Sự Cố

"Nếu mọi thứ bị lộn xộn — đừng panic. Copilot có tính năng **Checkpoints**:"

1. Trong Chat panel, tìm phần **Checkpoints**
2. Revert về checkpoint trước "TDD Red" bắt đầu
3. Thử lại với prompt được điều chỉnh

---

### Task 4.2 — Card Deck Mode (Design-Driven)

> **[Giới thiệu Pixel Jam agent]**

"**Pixel Jam** là agent tập trung vào UI/UX — nó ưu tiên design trước, logic sau."

**Tính năng sẽ xây dựng:** Card Deck Shuffle — người chơi tap để lật một card ngẫu nhiên, xem câu hỏi, thảo luận.

**Hướng dẫn:**
1. Mở Chat mới → chọn agent **Pixel Jam**
2. Nhập:
   ```
   New mode: Card Deck Shuffle. Every player opens the game, 
   taps, and gets a random card with a question
   ```
3. Quan sát agent tập trung vào visual design
4. Tinh chỉnh thêm:
   ```
   Add a cool 3D flip animation when revealing the card
   ```
   ```
   Make the card styling match the cyberpunk theme
   ```
5. Commit khi hài lòng

---

### Task 4.3 — UX Review Agent

> **[Kết hợp MCP tools + custom workflow]**

"Phần cuối cùng: chạy UX review tự động."

1. Chat mới → chọn **Pixel Jam**
2. Nhập:
   ```
   Run review
   ```
3. Khi được hỏi về Playwright tool access → **Allow for this Workspace**
4. Agent sẽ:
   - Chụp screenshot từng page
   - Phân tích usability issues
   - Kiểm tra accessibility
   - Review visual consistency

**Sau khi review xong, thực hiện follow-up:**
```
File the critical findings as GitHub issues
```
```
Fix the accessibility issues you found
```

---

## Kết Thúc

> **[Nói với khán giả]**

"Trong vòng một tiếng vừa qua, mọi người đã thực hành được bốn kỹ năng quan trọng:

| Kỹ năng | Bài học |
|---------|---------|
| **Context Engineering** | Dạy AI về codebase của mình — instructions file là investment một lần, lợi ích lâu dài |
| **Agentic Primitives** | Background agents để làm việc song song, Cloud agents cho tác vụ async dài hơi |
| **Design-First** | Plan Mode + iteration — đừng vội implement, hãy review plan trước |
| **TDD với Agents** | Red → Green → Refactor vẫn là quy trình đúng, agents chỉ giúp tăng tốc từng phase |

Điều quan trọng nhất tôi muốn mọi người mang về là: **AI không thay thế tư duy của bạn**. Nó cần bạn chỉ hướng, review, và quyết định. Bạn là kiến trúc sư — AI là công cụ xây dựng cực kỳ nhanh."

---

### 🎁 Bonus Challenges (Nếu Còn Thời Gian)

| Thử thách | Hướng tiếp cận |
|-----------|----------------|
| Fix UX issues từ review | Delegate cho background/cloud agent |
| Multiple themes | Thêm theme picker vào start screen |
| Social sharing | Thêm share button vào win state |
| Leaderboard | Track và hiển thị high scores |

---

### 🔗 Tài Nguyên Tham Khảo

- 📺 [VS Code YouTube](https://www.youtube.com/code)
- 📖 [VS Code Copilot Docs](https://code.visualstudio.com/docs/copilot/overview)
- 🌟 [Awesome Copilot](https://github.com/github/awesome-copilot)
- 💻 [Repo gốc](https://github.com/madebygps/vscode-github-copilot-agent-lab)

---

## 📌 Quick Reference — Những Điểm Cần Nhớ Khi Trình Bày

| Tình huống | Hành động |
|------------|-----------|
| Repo không có "Use this template" | **Fork** repo thay thế |
| Cần dùng Cloud Agent | Cài **GitHub MCP** trong VS Code trước |
| Chưa đăng nhập GitHub | Account icon → Sign in with GitHub |
| Bật Cloud Agent | Chat panel `+` → New cloud agent |
| Cloud Agent lấy sai owner | Kiểm tra URL trong output, cancel và chạy lại nếu sai |
| Cloud Agent hoàn thành | Mở **GitHub web** → xem và review Pull Request được tạo tự động |
| Code bị lộn xộn | Dùng **Checkpoints** trong Chat để revert |

---

*Happy Coding! 👋 — GitHub Dev Days Vietnam*
