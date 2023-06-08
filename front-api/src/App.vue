<template>
  <div>
    <div id="canvas">
      <div id="background"></div>
    </div>
    <div id="toolbox">
      <div id="toolbox-search">
        <input type="text" placeholder="Search tool..." />
      </div>
      <div id="tool-container">
        <div class="tool square"></div>
        <div class="tool triangle"></div>
        <div class="tool rectangle"></div>
      </div>
    </div>
    <div id="contextMenu" class="context-menu">
      <ul>
        <li id="delete">Delete</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  mounted() {
    const canvas = document.querySelector("#canvas");
    const tools = document.querySelectorAll(".tool");
    const contextMenu = document.querySelector("#contextMenu");
    const deleteMenuItem = document.querySelector("#delete");

    let currentTool = null;
    let isMoving = false;
    let initialX = 0;
    let initialY = 0;

    tools.forEach((tool) => {
      tool.addEventListener("mousedown", (event) => {
        if (event.target.tagName === "INPUT") {
          return;
        }

        const clone = tool.cloneNode(true);
        clone.style.position = "absolute";
        clone.style.left = event.clientX + "px";
        clone.style.top = event.clientY + "px";
        canvas.appendChild(clone);

        let selectedTool = clone;

        isMoving = true;
        initialX = event.clientX - selectedTool.offsetLeft;
        initialY = event.clientY - selectedTool.offsetTop;

        function handleMove(event) {
          if (isMoving) {
            const newLeft = event.clientX - initialX;
            const newTop = event.clientY - initialY;

            selectedTool.style.left = newLeft + "px";
            selectedTool.style.top = newTop + "px";
          }
        }

        function handleUp() {
          isMoving = false;
          canvas.removeEventListener("mousemove", handleMove);
          canvas.removeEventListener("mouseup", handleUp);
        }

        function handleToolMove(event) {
          event.stopPropagation();
          isMoving = true;
          initialX = event.clientX - selectedTool.offsetLeft;
          initialY = event.clientY - selectedTool.offsetTop;
        }

        selectedTool.addEventListener("mousedown", handleToolMove);
        selectedTool.addEventListener("contextmenu", (event) => {
          event.preventDefault();

          const posX = event.clientX;
          const posY = event.clientY;

          contextMenu.style.display = "block";
          contextMenu.style.left = posX + "px";
          contextMenu.style.top = posY + "px";

          currentTool = selectedTool;
        });

        canvas.addEventListener("mousemove", handleMove);
        canvas.addEventListener("mouseup", handleUp);

        event.stopPropagation();
      });
    });

    deleteMenuItem.addEventListener("click", () => {
      if (currentTool) {
        currentTool.remove();
      }
      contextMenu.style.display = "none";
      currentTool = null;
    });

    document.addEventListener("mousedown", (event) => {
      if (!event.target.closest(".context-menu")) {
        contextMenu.style.display = "none";
      }
    });
  },
};
</script>

<style scoped>
body {
  background: rgb(0, 75, 121);
  background: linear-gradient(172deg,
      rgba(0, 75, 121, 1) 0%,
      rgba(0, 19, 42, 1) 100%);
  margin: 0;
  padding: 0;
}

#canvas {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: auto;
}

#background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="20%" height="20%"><defs><pattern id="smallGrid" width="8" height="8" patternUnits="userSpaceOnUse"><path d="M 8 0 L 0 0 0 8" fill="none" stroke="gray" stroke-width="0.5"/></pattern><pattern id="grid" width="100%" height="100%" patternUnits="userSpaceOnUse"><rect width="100%" height="100%" fill="url(%23smallGrid)"/><path d="M 100% 0 L 0 0 0 100%" fill="none" stroke="gray" stroke-width="1"/></pattern></defs><rect width="100%" height="100%" fill="url(%23grid)"/></svg>');
  opacity: 0.5;
}

#toolbox {
  width: 200px;
  min-height: 50vh;
  background-color: #222;
  position: fixed;
  top: 50%;
  left: 0;
  transform: translateY(-50%);
  z-index: 9999;
  padding: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  border-radius: 4px;
}

#toolbox-search {
  width: 100%;
  padding: 10px;
  border-radius: 8px;
  margin-bottom: 10px;
  font-family: Arial, sans-serif;
  font-size: 14px;
}

#toolbox-search input {
  width: 100%;
  padding: 5px;
  border: 1px solid #555;
  border-radius: 4px;
  font-family: Arial, sans-serif;
  font-size: 14px;
  box-sizing: border-box;
  background-color: #444;
  color: #fff;
}

#tool-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
}

.tool {
  width: 60px;
  height: 60px;
  background-color: #6c6c6c;
  margin-bottom: 10px;
  cursor: move;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  user-select: none;
}

.tool:hover {
  background-color: #333;
}

.tool.triangle {
  clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
}

.tool.rectangle {
  width: 80px;
  height: 40px;
}

.context-menu {
  background-color: #333;
  border: 1px solid #555;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  border-radius: 4px;
  padding: 8px;
  position: absolute;
  z-index: 99999;
  font-family: Arial, sans-serif;
  font-size: 14px;
  display: none;
  color: #fff;
}

.context-menu ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.context-menu ul li {
  cursor: pointer;
  padding: 6px 12px;
}

.context-menu ul li:hover {
  background-color: #444;
}
</style>
