<script>
    let limiteSuperior = $state('');
    let limiteInferior = $state('');
    let formula = $state('');
    let terminos = $state([]);
    let errorMsg = $state('');

    let sumatoria = $derived(terminos.reduce((acc, t) => acc + t.valor, 0));
    let productoria = $derived(terminos.reduce((acc, t) => acc * t.valor, 1));

    function evaluarFormula(formulaStr, k) {
        try {
            const expr = formulaStr
                .replaceAll('^', '**')
                .replaceAll('k', `(${k})`);
            return Function(`return ${expr}`)();
        } catch {
            return null;
        }
    }

    function generarSucesion() {
        errorMsg = '';
        terminos = [];

        const m = parseInt(limiteInferior);
        const n = parseInt(limiteSuperior);

        if (isNaN(m) || isNaN(n)) {
            errorMsg = 'Los límites deben ser números enteros.';
            return;
        }
        if (m > n) {
            errorMsg = 'El límite inferior no puede ser mayor al superior.';
            return;
        }
        if (!formula.trim()) {
            errorMsg = 'Ingresa una fórmula.';
            return;
        }

        const resultado = [];
        for (let k = m; k <= n; k++) {
            const valor = evaluarFormula(formula, k);
            if (valor === null || !isFinite(valor)) {
                errorMsg = `Error al evaluar la fórmula en k=${k}. Revisa la sintaxis.`;
                terminos = [];
                return;
            }
            resultado.push({ k, valor });
        }

        terminos = resultado;
    }
</script>

<main class="flex min-h-screen items-center justify-center bg-base-200 p-4">
    <div class="card w-full max-w-4xl bg-base-100 shadow-xl">
        <div class="card-body gap-6">
            <h1 class="card-title text-3xl font-bold">Sucesiones</h1>

            <div class="flex flex-row gap-4">
                <fieldset class="fieldset flex-1">
                    <legend class="fieldset-legend">Límite inferior (m)</legend>
                    <input
                        type="number"
                        placeholder="Ej: 1"
                        class="input input-primary w-full"
                        bind:value={limiteInferior}
                    />
                </fieldset>

                <fieldset class="fieldset flex-1">
                    <legend class="fieldset-legend">Límite superior (n)</legend>
                    <input
                        type="number"
                        placeholder="Ej: 10"
                        class="input input-primary w-full"
                        bind:value={limiteSuperior}
                    />
                </fieldset>

                <fieldset class="fieldset flex-1">
                    <legend class="fieldset-legend">Fórmula (en términos de k)</legend>
                    <input
                        type="text"
                        placeholder="Ej: 1/k  o  k^2  o  2*k+1"
                        class="input input-primary w-full font-mono"
                        bind:value={formula}
                    />
                </fieldset>
            </div>

            <button class="btn btn-primary" onclick={generarSucesion}>Generar</button>

            {#if errorMsg}
                <div class="alert alert-error text-sm">{errorMsg}</div>
            {/if}

            {#if terminos.length > 0}
                <!-- Términos -->
                <div class="divider">Desarrollo de la sucesión</div>

                <div class="flex flex-wrap gap-2">
                    {#each terminos as t}
                        <div class="flex flex-col items-center rounded-xl bg-base-200 px-4 py-2 min-w-16">
                            <span class="text-xs text-secondary font-mono">k={t.k}</span>
                            <span class="font-bold text-primary font-mono">
                                {Number.isInteger(t.valor) ? t.valor : t.valor.toFixed(4)}
                            </span>
                        </div>
                    {/each}
                </div>

                <!-- Sumatoria y Productoria -->
                <div class="divider">Resultados</div>

                <div class="grid grid-cols-2 gap-4">
                    <div class="rounded-xl bg-base-200 p-4 flex flex-col gap-1">
                        <span class="text-sm text-primary">Sumatoria (Σ)</span>
                        <span class="text-2xl font-bold text-primary font-mono">
                            {Number.isInteger(sumatoria) ? sumatoria : sumatoria.toFixed(6)}
                        </span>
                        <span class="text-xs text-secondary font-mono">
                            {terminos.map(t => Number.isInteger(t.valor) ? t.valor : t.valor.toFixed(4)).join(' + ')}
                        </span>
                    </div>

                    <div class="rounded-xl bg-base-200 p-4 flex flex-col gap-1">
                        <span class="text-sm text-primary">Productoria (∏)</span>
                        <span class="text-2xl font-bold text-secondary font-mono">
                            {Number.isInteger(productoria) ? productoria : productoria.toFixed(6)}
                        </span>
                        <span class="text-xs text-secondary font-mono">
                            {terminos.map(t => Number.isInteger(t.valor) ? t.valor : t.valor.toFixed(4)).join(' × ')}
                        </span>
                    </div>
                </div>
            {/if}
        </div>
    </div>
</main>