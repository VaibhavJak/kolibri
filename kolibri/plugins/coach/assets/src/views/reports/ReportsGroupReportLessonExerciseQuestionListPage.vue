<template>

  <CoachAppBarPage
    :authorized="userIsAuthorized"
    authorizedRole="adminOrCoach"
    :showSubNav="true"
  >

    <KPageContainer>

      <ReportsResourceHeader
        :resource="exercise"
        @previewClick="onPreviewClick"
      />

      <ReportsControls @export="exportCSV" />

      <CoreTable :emptyMessage="coachString('questionListEmptyState')">
        <template #headers>
          <th>{{ coachString('questionLabel') }}</th>
          <th>{{ coachString('helpNeededLabel') }}</th>
        </template>
        <template #tbody>
          <transition-group
            tag="tbody"
            name="list"
          >
            <tr
              v-for="tableRow in table"
              :key="tableRow.question_id"
            >
              <td>
                <KRouterLink
                  :text="tableRow.title"
                  :to="questionLink(tableRow.question_id)"
                  icon="person"
                />
              </td>
              <td>
                <LearnerProgressRatio
                  :verb="VERBS.needHelp"
                  :icon="ICONS.help"
                  :total="tableRow.total"
                  :count="tableRow.total - tableRow.correct"
                  :verbosity="1"
                />
              </td>
            </tr>
          </transition-group>
        </template>
      </CoreTable>
    </KPageContainer>
  </CoachAppBarPage>

</template>


<script>

  import { mapGetters, mapState } from 'vuex';
  import commonCoach from '../common';
  import CoachAppBarPage from '../CoachAppBarPage';
  import LearnerProgressRatio from '../common/status/LearnerProgressRatio';
  import CSVExporter from '../../csv/exporter';
  import * as csvFields from '../../csv/fields';
  import ReportsResourceHeader from './ReportsResourceHeader';
  import ReportsControls from './ReportsControls';
  import { PageNames } from './../../constants';

  export default {
    name: 'ReportsGroupReportLessonExerciseQuestionListPage',
    components: {
      CoachAppBarPage,
      ReportsResourceHeader,
      ReportsControls,
      LearnerProgressRatio,
    },
    mixins: [commonCoach],
    computed: {
      ...mapState('questionList', ['exercise']),
      ...mapGetters('questionList', ['difficultQuestions']),
      lesson() {
        return this.lessonMap[this.$route.params.lessonId];
      },
      resource() {
        return this.contentMap[this.$route.params.exerciseId];
      },
      group() {
        return this.groupMap[this.$route.params.groupId];
      },
      table() {
        return this.difficultQuestions.map(question => {
          const tableRow = {};
          Object.assign(tableRow, question);
          return tableRow;
        });
      },
    },
    methods: {
      questionLink(questionId) {
        return this.classRoute(PageNames.REPORTS_GROUP_REPORT_LESSON_EXERCISE_QUESTION_PAGE_ROOT, {
          questionId,
          exerciseId: this.$route.params.exerciseId,
        });
      },
      exportCSV() {
        const columns = [
          {
            name: this.coachString('questionLabel'),
            key: 'title',
          },
          ...csvFields.helpNeeded(),
        ];

        const exporter = new CSVExporter(columns, this.className);
        exporter.addNames({
          group: this.group.name,
          lesson: this.lesson.title,
          resource: this.resource.title,
          difficultQuestions: this.coachString('difficultQuestionsLabel'),
        });

        exporter.export(this.table);
      },
      onPreviewClick() {
        this.$router.push(
          this.$router.getRoute(
            'RESOURCE_CONTENT_PREVIEW',
            {
              contentId: this.exercise.id,
            },
            this.defaultBackLinkQuery
          )
        );
      },
    },
  };

</script>


<style lang="scss" scoped>

  @import '../common/print-table';

  .stats {
    margin-right: 16px;
  }

</style>
