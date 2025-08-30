<script>
  import { onMount } from 'svelte';
  
  let leftPupil;
  let rightPupil;
  let yesButton;
  let noButton;
  let mouthExpression = 'neutral';

  function getEyePosition(eye) {
    if (!eye) return { x: 0, y: 0 };
    const rect = eye.parentElement.getBoundingClientRect();
    return {
      x: rect.left + rect.width / 2,
      y: rect.top + rect.height / 2
    };
  }

  function updateEyes(mouseX, mouseY) {
    if (!leftPupil || !rightPupil) return;

    // Update left eye
    const leftEyePos = getEyePosition(leftPupil);
    const leftAngle = Math.atan2(mouseY - leftEyePos.y, mouseX - leftEyePos.x);
    const leftDistance = Math.min(15, Math.hypot(mouseX - leftEyePos.x, mouseY - leftEyePos.y) * 0.05);
    const leftX = Math.cos(leftAngle) * leftDistance;
    const leftY = Math.sin(leftAngle) * leftDistance;
    leftPupil.style.transform = `translate(calc(-50% + ${leftX}px), calc(-50% + ${leftY}px))`;

    // Update right eye
    const rightEyePos = getEyePosition(rightPupil);
    const rightAngle = Math.atan2(mouseY - rightEyePos.y, mouseX - rightEyePos.x);
    const rightDistance = Math.min(15, Math.hypot(mouseX - rightEyePos.x, mouseY - rightEyePos.y) * 0.05);
    const rightX = Math.cos(rightAngle) * rightDistance;
    const rightY = Math.sin(rightAngle) * rightDistance;
    rightPupil.style.transform = `translate(calc(-50% + ${rightX}px), calc(-50% + ${rightY}px))`;
  }

  function checkMouthExpression(mouseX, mouseY) {
    if (!yesButton || !noButton) return;

    const yesRect = yesButton.getBoundingClientRect();
    const noRect = noButton.getBoundingClientRect();

    const isOverYes = mouseX >= yesRect.left && mouseX <= yesRect.right && 
                     mouseY >= yesRect.top && mouseY <= yesRect.bottom;
    const isOverNo = mouseX >= noRect.left && mouseX <= noRect.right && 
                    mouseY >= noRect.top && mouseY <= noRect.bottom;

    if (isOverYes) {
      mouthExpression = 'smile';
    } else if (isOverNo) {
      mouthExpression = 'frown';
    } else {
      mouthExpression = 'neutral';
    }
  }

  onMount(() => {
    const handleMouseMove = (e) => {
      updateEyes(e.clientX, e.clientY);
      checkMouthExpression(e.clientX, e.clientY);
    };

    const handleTouchMove = (e) => {
      if (e.touches.length > 0) {
        updateEyes(e.touches[0].clientX, e.touches[0].clientY);
        checkMouthExpression(e.touches[0].clientX, e.touches[0].clientY);
      }
    };

    document.addEventListener('mousemove', handleMouseMove);
    document.addEventListener('touchmove', handleTouchMove);

    // Initialize eyes looking forward
    updateEyes(window.innerWidth / 2, window.innerHeight / 2);

    return () => {
      document.removeEventListener('mousemove', handleMouseMove);
      document.removeEventListener('touchmove', handleTouchMove);
    };
  });
</script>

<div class="min-h-screen bg-gradient-to-br from-purple-400 via-pink-500 to-red-500 flex items-center justify-center overflow-hidden">
  <div class="relative">
    <!-- Background decoration -->
    <div class="absolute -top-20 -left-20 w-40 h-40 bg-white bg-opacity-20 rounded-full blur-xl"></div>
    <div class="absolute -bottom-16 -right-16 w-32 h-32 bg-yellow-300 bg-opacity-30 rounded-full blur-lg"></div>
    <div class="absolute top-10 right-10 w-24 h-24 bg-blue-300 bg-opacity-25 rounded-full blur-md"></div>
    
    <!-- Main container -->
    <div class="bg-white bg-opacity-10 backdrop-blur-lg rounded-3xl p-12 shadow-2xl border border-white border-opacity-20">
      <div class="text-center mb-8">
        <h1 class="text-4xl font-bold text-gray-900 mb-4 drop-shadow-lg bg-white bg-opacity-90 rounded-lg px-4 py-2">Cursor Tracking Eyes</h1>
        <p class="text-gray-800 text-lg font-semibold bg-white bg-opacity-80 rounded-lg px-3 py-1">Watch me follow your cursor!</p>
      </div>
      
      <!-- Face container -->
      <div class="face-container floating">
        <!-- Eyes container -->
        <div class="flex justify-center items-center gap-8 mb-6">
          <div class="eye-container relative">
            <div class="eye w-32 h-32 bg-white rounded-full border-4 border-gray-300 shadow-2xl">
              <div class="pupil" bind:this={leftPupil}></div>
            </div>
          </div>
          <div class="eye-container relative">
            <div class="eye w-32 h-32 bg-white rounded-full border-4 border-gray-300 shadow-2xl">
              <div class="pupil" bind:this={rightPupil}></div>
            </div>
          </div>
        </div>
        
        <!-- Nose -->
        <div class="flex justify-center mb-4">
          <div class="nose w-6 h-8 bg-pink-300 rounded-full shadow-lg border-2 border-pink-400"></div>
        </div>
        
        <!-- Mouth -->
        <div class="flex justify-center items-center h-12">
          {#if mouthExpression === 'smile'}
            <div class="mouth-smile w-20 h-10 border-4 border-black border-t-0 rounded-b-full"></div>
          {:else if mouthExpression === 'frown'}
            <div class="mouth-frown w-20 h-10 border-4 border-black border-b-0 rounded-t-full"></div>
          {:else}
            <div class="mouth-neutral w-16 h-1 bg-black rounded-full"></div>
          {/if}
        </div>
      </div>
      
      <!-- YES/NO Buttons -->
      <div class="flex justify-center gap-12 mt-8">
        <button 
          bind:this={yesButton}
          class="yes-button bg-green-500 hover:bg-green-600 text-white font-bold text-2xl px-8 py-4 rounded-lg shadow-2xl border-4 border-green-600 transition-all duration-200 hover:scale-105"
        >
          YES
        </button>
        <button 
          bind:this={noButton}
          class="no-button bg-red-500 hover:bg-red-600 text-white font-bold text-2xl px-8 py-4 rounded-lg shadow-2xl border-4 border-red-600 transition-all duration-200 hover:scale-105"
        >
          NO
        </button>
      </div>
      
      <!-- Instructions -->
      <div class="text-center mt-8">
        <p class="text-gray-800 text-sm font-semibold bg-white bg-opacity-80 rounded-lg px-3 py-1">Hover over YES or NO to change my expression!</p>
      </div>
    </div>
    
    <!-- Floating particles -->
    <div class="absolute top-0 left-0 w-2 h-2 bg-white rounded-full opacity-60 animate-ping" style="animation-delay: 0s;"></div>
    <div class="absolute top-1/4 right-0 w-1 h-1 bg-yellow-300 rounded-full opacity-80 animate-ping" style="animation-delay: 1s;"></div>
    <div class="absolute bottom-1/4 left-0 w-1.5 h-1.5 bg-pink-300 rounded-full opacity-70 animate-ping" style="animation-delay: 2s;"></div>
  </div>
</div>

<style>
  .eye {
    position: relative;
    transition: transform 0.1s ease;
  }
  
  .pupil {
    position: absolute;
    width: 24px;
    height: 24px;
    background: #000;
    border-radius: 50%;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    transition: transform 0.1s ease;
  }
  
  @keyframes float {
    0%, 100% { transform: translateY(0px); }
    50% { transform: translateY(-10px); }
  }
  
  .floating {
    animation: float 3s ease-in-out infinite;
  }
</style>