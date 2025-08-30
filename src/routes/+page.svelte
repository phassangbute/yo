<script>
  import { onMount } from 'svelte';
  
  let leftPupil;
  let rightPupil;

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
    const leftDistance = Math.min(8, Math.hypot(mouseX - leftEyePos.x, mouseY - leftEyePos.y) * 0.05);
    const leftX = Math.cos(leftAngle) * leftDistance;
    const leftY = Math.sin(leftAngle) * leftDistance;
    leftPupil.style.transform = `translate(calc(-50% + ${leftX}px), calc(-50% + ${leftY}px))`;

    // Update right eye
    const rightEyePos = getEyePosition(rightPupil);
    const rightAngle = Math.atan2(mouseY - rightEyePos.y, mouseX - rightEyePos.x);
    const rightDistance = Math.min(8, Math.hypot(mouseX - rightEyePos.x, mouseY - rightEyePos.y) * 0.05);
    const rightX = Math.cos(rightAngle) * rightDistance;
    const rightY = Math.sin(rightAngle) * rightDistance;
    rightPupil.style.transform = `translate(calc(-50% + ${rightX}px), calc(-50% + ${rightY}px))`;
  }

  onMount(() => {
    const handleMouseMove = (e) => {
      updateEyes(e.clientX, e.clientY);
    };

    const handleTouchMove = (e) => {
      if (e.touches.length > 0) {
        updateEyes(e.touches[0].clientX, e.touches[0].clientY);
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
        <h1 class="text-4xl font-bold text-white mb-4 drop-shadow-lg">Eye Tracking Emoji</h1>
        <p class="text-white text-opacity-80 text-lg">Watch me follow your cursor!</p>
      </div>
      
      <!-- Emoji container -->
      <div class="flex justify-center items-center floating">
        <div class="face relative" style="font-size: 12rem;">
          <span class="text-yellow-400 drop-shadow-2xl">😊</span>
          <!-- Custom eyes that will replace emoji eyes -->
          <div class="absolute top-[3.2rem] left-[3.4rem] w-6 h-6">
            <div class="eye w-6 h-6 bg-white rounded-full border-2 border-gray-300">
              <div class="pupil" bind:this={leftPupil}></div>
            </div>
          </div>
          <div class="absolute top-[3.2rem] right-[3.4rem] w-6 h-6">
            <div class="eye w-6 h-6 bg-white rounded-full border-2 border-gray-300">
              <div class="pupil" bind:this={rightPupil}></div>
            </div>
          </div>
        </div>
      </div>
      
      <!-- Instructions -->
      <div class="text-center mt-8">
        <p class="text-white text-opacity-70 text-sm">Move your mouse around to see the magic!</p>
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
    width: 8px;
    height: 8px;
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