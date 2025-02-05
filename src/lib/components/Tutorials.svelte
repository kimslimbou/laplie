<script lang="ts">
  import { base } from "$app/paths";
  import type { ExternalTutorial, Link, Tutorial } from "$lib/types";
  import account from "@material-design-icons/svg/outlined/account_box.svg?raw";
  import email from "@material-design-icons/svg/outlined/email.svg?raw";
  import download from "@material-design-icons/svg/outlined/file_download.svg?raw";
  import help_outline from "@material-design-icons/svg/outlined/help_outline.svg?raw";
  import insert_drive_file from "@material-design-icons/svg/outlined/insert_drive_file.svg?raw";
  import SvgIcon from "./SvgIcon.svelte";

  export let tutorials: Tutorial[];
  export let links: Link[];

  const selectedFilters: Record<string, boolean> = links.reduce(
    (acc, link) => ({ ...acc, [link.slug]: false }),
    {}
  );

  let noFilterSelected = true;

  const onClickFilter = (slug: string) => {
    selectedFilters[slug] = !selectedFilters[slug];
    noFilterSelected = !hasAnyFilterActive();
  };

  const hasAnyFilterActive = () =>
    Object.values(selectedFilters).some((value) => value);

  const icons = [
    {
      key: "email",
      src: email,
    },
    {
      key: "fichier",
      src: insert_drive_file,
    },
    {
      key: "point d'interrogation",
      src: help_outline,
    },
    {
      key: "compte",
      src: account,
    },
    {
      key: "télécharger",
      src: download,
    },
  ];
  const findIconSrc = (iconKey: string) => {
    return icons.find((i) => i.key === iconKey)?.src || "";
  };

  const isExternalTutorial = (
    tutorial: Tutorial
  ): tutorial is ExternalTutorial => "url" in tutorial;
</script>

<section class="tutorials--container">
  <h2 class="tutorials--title">
    <SvgIcon src={insert_drive_file} />Tutoriels
  </h2>
  <h3>Savoir utiliser les services publics</h3>

  <div class="tutorials--filters">
    <span class="tutorials--filters-instruction"
      >Filtrer les tutoriels par service</span
    >
    <div>
      {#each links as { title }}
        <button
          class="tutorials--filter"
          class:tutorials--filter-selected={selectedFilters[title]}
          on:click={() => onClickFilter(title)}
        >
          {title}
        </button>
      {/each}
    </div>
  </div>

  <div class="tutorials--list-container">
    <div class="tutorials--list">
      {#each tutorials as tutorial}
        {#if selectedFilters[tutorial.service] || noFilterSelected}
          <a
            href={isExternalTutorial(tutorial)
              ? tutorial.url
              : `${base}/tutorials/${tutorial.slug}`}
            target={isExternalTutorial(tutorial) ? "_blank" : undefined}
            class="tutorial--link"
          >
            {#if tutorial.icon}
              <SvgIcon
                class="tutorial--icon"
                src={findIconSrc(tutorial.icon)}
              />
            {/if}
            <span class="tutorial--title">{tutorial.title}</span>
          </a>
        {/if}
      {/each}
    </div>
  </div>
</section>

<style lang="scss">
  .tutorials--title {
    align-items: center;
    display: inline-flex;
    gap: 1rem;
  }

  .tutorials--filters {
    align-items: flex-start;
    display: flex;
    flex-direction: column;
  }

  .tutorials--filters-instruction {
    color: var(--color-blue-dark);
    margin-right: 1em;
  }

  .tutorials--filter {
    align-self: center;
    background-color: inherit;
    border-color: var(--color-blue);
    border-radius: 15px;
    border-style: solid;
    border-width: 1px;
    cursor: pointer;
    display: inline-flex;
    margin-right: 1.2em;
    margin-top: 1em;
    padding: 5px 12px 5px 12px;
  }

  .tutorials--filter-selected {
    align-self: center;
    background-color: blue;
    border-color: blue;
    border-radius: 15px;
    border-style: solid;
    border-width: 1px;
    color: white;
    cursor: pointer;
    display: inline-flex;
    margin-right: 1.2em;
    padding: 5px 12px 5px 12px;
  }

  .tutorials--list {
    display: flex;
    flex-wrap: wrap;
    overflow: auto;
    white-space: normal;

    @include md {
      white-space: nowrap;
    }
  }

  .tutorial--link {
    align-items: center;
    align-self: stretch;
    background-color: var(--alt-bg-color);
    border-radius: 10px;
    display: inline-flex;
    justify-content: space-between;
    margin: 1em 1em 0 0;
    max-width: 13rem;
    min-height: 4.75rem;
    padding: 1em;
    text-decoration: none;
  }

  :global(.tutorial--icon) {
    margin-right: 1rem;
  }

  .tutorial--title {
    white-space: normal;
  }
</style>
