// ==UserScript==
// @name         YouTube Enhancer
// @namespace    http://tampermonkey.net/
// @version      0.2
// @description  Elimina recomendaciones no deseadas y añade un botón para mostrar videos guardados en YouTube
// @author       Tu Nombre
// @match        *://www.youtube.com/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    // Función para eliminar recomendaciones no deseadas
    function eliminarRecomendaciones() {
        try {
            const recomendaciones = document.querySelectorAll('#dismissible');
            recomendaciones.forEach((recomendacion) => {
                if (recomendacion.querySelector('ytd-thumbnail-overlay-time-status-renderer')) {
                    recomendacion.remove();
                }
            });
            console.log('Recomendaciones no deseadas eliminadas');
        } catch (error) {
            console.error('Error al eliminar recomendaciones:', error);
        }
    }

    // Función para añadir un botón de videos guardados
    function añadirBotonVideosGuardados() {
        if (document.getElementById('botonVideosGuardados')) {
            return; // Evita crear múltiples botones
        }

        const botonVideosGuardados = document.createElement('button');
        botonVideosGuardados.id = 'botonVideosGuardados';
        botonVideosGuardados.textContent = 'Ver Videos Guardados';
        botonVideosGuardados.style.position = 'fixed';
        botonVideosGuardados.style.bottom = '10px';
        botonVideosGuardados.style.right = '10px';
        botonVideosGuardados.style.zIndex = '1000';
        botonVideosGuardados.style.padding = '10px';
        botonVideosGuardados.style.backgroundColor = '#FF0000';
        botonVideosGuardados.style.color = '#fff';
        botonVideosGuardados.style.border = 'none';
        botonVideosGuardados.style.cursor = 'pointer';

        botonVideosGuardados.addEventListener('click', () => {
            const videosGuardados = document.querySelectorAll('[data-sessionlink="feature=playlist"]');
            if (videosGuardados.length > 0) {
                alert(`Tienes ${videosGuardados.length} videos guardados.`);
            } else {
                alert('No tienes videos guardados.');
            }
        });

        document.body.appendChild(botonVideosGuardados);
    }

    // Iniciar la eliminación y añadir el botón al cargar la página
    window.addEventListener('load', function() {
        eliminarRecomendaciones();
        añadirBotonVideosGuardados();

        // Configurar un intervalo para ejecutar cada 2 segundos
        setInterval(() => {
            eliminarRecomendaciones();
            añadirBotonVideosGuardados();
        }, 2000);
    });

    // Observador de cambios en la página
    const observador = new MutationObserver(() => {
        eliminarRecomendaciones();
        añadirBotonVideosGuardados();
    });

    observador.observe(document.body, { childList: true, subtree: true });
})();
