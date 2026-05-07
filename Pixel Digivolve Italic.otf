const navLinks = document.querySelectorAll(".top-nav a");

navLinks.forEach((link) => {
  link.addEventListener("mouseenter", () => {
    link.dataset.label = link.textContent;
    link.textContent = `* ${link.dataset.label} *`;
  });

  link.addEventListener("mouseleave", () => {
    if (link.dataset.label) {
      link.textContent = link.dataset.label;
    }
  });
});

const cards = document.querySelectorAll(".project-card, .project-detail, .thumbnail-card");

cards.forEach((card) => {
  card.addEventListener("mousemove", (event) => {
    const rect = card.getBoundingClientRect();
    const x = event.clientX - rect.left;
    const y = event.clientY - rect.top;
    card.style.setProperty("--mouse-x", `${x}px`);
    card.style.setProperty("--mouse-y", `${y}px`);
  });
});

const calendarTitle = document.querySelector("[data-calendar-title]");
const calendarGrid = document.querySelector("[data-calendar-grid]");

if (calendarTitle && calendarGrid) {
  const renderCalendar = () => {
    const today = new Date();
    const year = today.getFullYear();
    const month = today.getMonth();
    const weekdays = ["S", "M", "T", "W", "T", "F", "S"];
    const firstDay = new Date(year, month, 1).getDay();
    const totalDays = new Date(year, month + 1, 0).getDate();
    const totalCells = Math.ceil((firstDay + totalDays) / 7) * 7;

    calendarTitle.textContent = new Intl.DateTimeFormat("en-US", {
      month: "long",
      year: "numeric",
    }).format(today);
    calendarGrid.innerHTML = "";

    weekdays.forEach((weekday) => {
      const dayName = document.createElement("span");
      dayName.className = "calendar-day-name";
      dayName.textContent = weekday;
      calendarGrid.append(dayName);
    });

    for (let index = 0; index < totalCells; index += 1) {
      const dateNumber = index - firstDay + 1;
      const day = document.createElement("span");

      if (dateNumber < 1 || dateNumber > totalDays) {
        day.className = "calendar-empty";
      } else {
        day.textContent = dateNumber;
        if (dateNumber === today.getDate()) {
          day.className = "today";
          day.setAttribute("aria-current", "date");
        }
      }

      calendarGrid.append(day);
    }

    const nextMidnight = new Date(year, month, today.getDate() + 1);
    window.setTimeout(renderCalendar, nextMidnight.getTime() - today.getTime() + 1000);
  };

  renderCalendar();
}

const counter = document.querySelector(".counter");

if (counter) {
  const storageKey = "softStitchVisitorCount";
  const resetKey = "softStitchVisitorResetId";
  const startCount = Number.parseInt(counter.dataset.start || "0", 10);
  const resetId = counter.dataset.resetId || "";
  const savedResetId = localStorage.getItem(resetKey) || "";
  const savedCount = Number.parseInt(localStorage.getItem(storageKey) || "", 10);
  const shouldReset = resetId && savedResetId !== resetId;
  const nextCount = shouldReset ? startCount : Number.isFinite(savedCount) ? savedCount + 1 : startCount;

  localStorage.setItem(storageKey, String(nextCount));
  localStorage.setItem(resetKey, resetId);
  counter.setAttribute("aria-label", `Visitor number ${nextCount}`);
  counter.innerHTML = "";

  String(nextCount)
    .padStart(5, "0")
    .split("")
    .forEach((digit) => {
      const slot = document.createElement("span");
      slot.textContent = digit;
      counter.appendChild(slot);
    });
}

const guestbookForm = document.querySelector(".guestbook-form");
const guestbookList = document.querySelector(".guestbook-list");
const guestbookPageInfo = document.querySelector(".guestbook-page-info");
const guestbookPageButtons = document.querySelectorAll(".guestbook-page-btn");

if (guestbookForm && guestbookList) {
  const guestbookKey = "softStitchGuestbookEntries";
  const guestbookResetKey = "softStitchGuestbookResetId";
  const guestbookResetId = guestbookList.dataset.resetId || "";
  const savedGuestbookResetId = localStorage.getItem(guestbookResetKey) || "";

  if (guestbookResetId && savedGuestbookResetId !== guestbookResetId) {
    localStorage.removeItem(guestbookKey);
    localStorage.setItem(guestbookResetKey, guestbookResetId);
  }

  const savedEntries = JSON.parse(localStorage.getItem(guestbookKey) || "[]");
  const entriesPerPage = 5;
  let currentPage = 1;

  const formatEntryDate = (createdAt) => {
    if (!createdAt) {
      return "";
    }

    const date = new Date(createdAt);
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, "0");
    const day = String(date.getDate()).padStart(2, "0");
    const hours = date.getHours();
    const minutes = String(date.getMinutes()).padStart(2, "0");
    const period = hours >= 12 ? "PM" : "AM";
    const displayHours = String(hours % 12 || 12).padStart(2, "0");

    return `${year}.${month}.${day} ${displayHours}:${minutes} ${period}`;
  };

  const renderEntry = ({ name, message, createdAt }) => {
    const entry = document.createElement("article");
    entry.className = "guestbook-entry";

    const author = document.createElement("strong");
    author.textContent = name;

    const date = document.createElement("time");
    date.textContent = formatEntryDate(createdAt);
    if (createdAt) {
      date.dateTime = createdAt;
    }

    const body = document.createElement("p");
    body.textContent = message;

    entry.append(author, date, body);
    guestbookList.append(entry);
  };

  const renderGuestbookPage = () => {
    const totalPages = Math.max(1, Math.ceil(savedEntries.length / entriesPerPage));
    currentPage = Math.min(Math.max(currentPage, 1), totalPages);
    const startIndex = (currentPage - 1) * entriesPerPage;
    const pageEntries = savedEntries.slice(startIndex, startIndex + entriesPerPage);

    guestbookList.innerHTML = "";
    pageEntries.forEach(renderEntry);

    if (guestbookPageInfo) {
      guestbookPageInfo.textContent = `${currentPage} / ${totalPages}`;
    }

    guestbookPageButtons.forEach((button) => {
      const action = button.dataset.pageAction;
      button.disabled = (action === "prev" && currentPage === 1) || (action === "next" && currentPage === totalPages);
    });
  };

  renderGuestbookPage();

  guestbookPageButtons.forEach((button) => {
    button.addEventListener("click", () => {
      currentPage += button.dataset.pageAction === "next" ? 1 : -1;
      renderGuestbookPage();
    });
  });

  guestbookForm.addEventListener("submit", (event) => {
    event.preventDefault();

    const formData = new FormData(guestbookForm);
    const entry = {
      name: String(formData.get("name") || "").trim(),
      message: String(formData.get("message") || "").trim(),
      createdAt: new Date().toISOString(),
    };

    if (!entry.name || !entry.message) {
      return;
    }

    savedEntries.unshift(entry);
    localStorage.setItem(guestbookKey, JSON.stringify(savedEntries));
    currentPage = 1;
    renderGuestbookPage();
    guestbookForm.reset();
  });
}

const projectBoards = document.querySelectorAll("[data-project-board]");

projectBoards.forEach((board) => {
  const projectCards = Array.from(board.querySelectorAll("[data-project-card]"));
  const pageInfo = board.querySelector(".project-page-info");
  const pageButtons = board.querySelectorAll(".project-page-btn");
  const itemsPerPage = 6;
  let currentPage = 1;

  const hash = window.location.hash.slice(1);
  const hashIndex = projectCards.findIndex((card) => card.id === hash);
  if (hashIndex >= 0) {
    currentPage = Math.floor(hashIndex / itemsPerPage) + 1;
  }

  const renderProjectPage = () => {
    const totalPages = Math.max(1, Math.ceil(projectCards.length / itemsPerPage));
    currentPage = Math.min(Math.max(currentPage, 1), totalPages);
    const startIndex = (currentPage - 1) * itemsPerPage;
    const endIndex = startIndex + itemsPerPage;

    projectCards.forEach((card, index) => {
      card.hidden = index < startIndex || index >= endIndex;
    });

    if (pageInfo) {
      pageInfo.textContent = `${currentPage} / ${totalPages}`;
    }

    pageButtons.forEach((button) => {
      const action = button.dataset.projectPageAction;
      button.disabled = (action === "prev" && currentPage === 1) || (action === "next" && currentPage === totalPages);
    });
  };

  renderProjectPage();

  if (hashIndex >= 0) {
    window.requestAnimationFrame(() => {
      projectCards[hashIndex].scrollIntoView({ block: "center" });
    });
  }

  pageButtons.forEach((button) => {
    button.addEventListener("click", () => {
      currentPage += button.dataset.projectPageAction === "next" ? 1 : -1;
      renderProjectPage();
      board.scrollIntoView({ behavior: "smooth", block: "start" });
    });
  });
});
