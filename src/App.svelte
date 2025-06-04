<script>
  import ScatterRatingGross from './ScatterRatingGross.svelte';
</script>

<style>
  main {
    padding: 2rem;
    font-family: Arial, sans-serif;
    background-color: #fafafa;
  }

  h1 {
    font-size: 2rem;
    margin-bottom: 1rem;
  }

  section {
    margin-bottom: 3rem;
    padding: 1rem;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.05);
  }
</style>

<main>
  <h1>IMDb Dashboard</h1>

  <section>
    <h2>Scatter Plot: Rating vs Gross</h2>
    <ScatterRatingGross />
  </section>

  <!-- Example placeholder for future charts -->
  <!--
  <section>
    <h2>Clustered Movie View</h2>
    <ScatterClusters />
  </section>
  -->
</main>
